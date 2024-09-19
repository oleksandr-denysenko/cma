
# HMAC-based Authentication and Request Signing

## Overview

To securely interact with our API, all clients must authenticate their requests using HMAC-based signatures. 
The signature is created using a secret key provided during token generation, ensuring the authenticity and integrity of each request. 
The process involves creating a signature that covers the request timestamp, HTTP method, request path, and the request body (if applicable).

## Headers for API Requests

Every API request must include the following headers:

- `X-Api-Token`: The token that is generated in the dashboard.
- `X-Api-Signature`: A request signature in HEX format, generated using the HMAC-SHA256 algorithm.
- `X-Api-Ts`: A timestamp representing the number of seconds since Unix Epoch (UTC).

## Request Signing Process

### 1. Concatenate the following information:

1. **Timestamp**: Value of the `X-Api-Ts` header, which is the number of seconds since Unix Epoch.
2. **HTTP Method**: The HTTP method in upper-case (e.g., GET, POST).
3. **Request Path**: The URI of the request, without the hostname, including any query parameters.
4. **Request Body**: The exact body of the request. If there is no body (e.g., for GET requests), omit this step. The body could be either:
   - JSON: for simple API requests like creating a customer.
   - Multipart (Binary + JSON): when uploading files, the entire multipart data, including binary content and form fields, must be signed.

### 2. Generate the HMAC Signature:

Use the concatenated string and apply the `HMAC-SHA256` algorithm with the secret key. 
The generated signature is added as the value of the `X-Api-Signature` header.

## Example: PHP Implementation

Here’s how to implement request signing in PHP using the **HMAC-SHA256** algorithm.

### HMAC Signature Utility

```php
function createHMACSignature($secretKey, $timestamp, $method, $path, $body = '') {
    $data = $timestamp . strtoupper($method) . $path . $body;
    return hash_hmac('sha256', $data, $secretKey);
}
```

### Example 1: Creating a Personal Application (Simple JSON Request)

This example demonstrates how to create a **personal application** with a simple JSON request, based on the updated path from the Swagger API.

```php
function createPersonalApplication($apiToken, $secretKey) {
    $timestamp = time();
    $path = "/onboarding/v1/partner/applications/personal";
    $bodyJson = json_encode([
        'email' => 'testcustomer@gmail.com',
        'phoneNumber' => '49876760018',
        'details' => [
            'firstName' => 'Partner',
            'lastName' => 'Customer',
            'nationalityIso3' => 'FIN',
            'dateOfBirth' => '1990-11-19',
            "occupation" => "Engineer",
            "annualTurnover" => "ZERO_TO_1000",
            "fundsSource" => "WINNINGS",
            'residentialAddress' => [
                'countryIso3' => 'CYP',
                'postalCode' => '3035',
                'city' => 'Limassol',
                'addressLine1' => 'Main str 94/1',
                'addressLine2' => 'optional'
            ]
        ]
    ]);
    
    // Generate signature
    $signature = createHMACSignature($secretKey, $timestamp, 'POST', $path, $bodyJson);

    $headers = [
        "X-Api-Token: $apiToken",
        "X-Api-Signature: $signature",
        "X-Api-Ts: $timestamp",
        'Content-Type: application/json'
    ];

    $ch = curl_init("https://v3.amaiz.com$path");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $bodyJson);
    curl_setopt($ch, CURLOPT_POST, true);

    $response = curl_exec($ch);
    curl_close($ch);

    return $response;
}

// Example usage
$apiToken = 'your_api_token';
$secretKey = 'your_secret_key';
$response = createPersonalApplication($apiToken, $secretKey);
echo $response;
```

### Example 2: Uploading a Document (Multipart Request)

This example demonstrates how to upload a document using a **multipart request**, based on the correct URL path for uploading personal documents from the Swagger API.

```php
function uploadDocument($apiToken, $secretKey, $applicantId, $filePath) {
    $timestamp = time();
    $path = "/onboarding/v1/partner/applications/personal/$applicantId/documents";
    
    // Query parameters for the document upload
    $queryParams = http_build_query([
        'type' => 'ID_CARD',
        'side' => 'FRONT',
        'issuingCountryIso3' => 'CYP'
    ]);
    
    // Combine path and query parameters for signature
    $fullPathWithQuery = "$path?$queryParams";

    $fullUrl = "https://v3.amaiz.com$fullPathWithQuery";

    $fileData = file_get_contents($filePath);
    $fileName = basename($filePath);

    // Create a multipart body
    $body = "--boundary\r\n" .
            "Content-Disposition: form-data; name=\"file\"; filename=\"$fileName\"\r\n" .
            "Content-Type: application/octet-stream\r\n\r\n" .
            $fileData . "\r\n" .
            "--boundary--\r\n";

    // Generate signature
    $signature = createHMACSignature($secretKey, $timestamp, 'POST', $fullPathWithQuery, $body);

    $headers = [
        "X-Api-Token: $apiToken",
        "X-Api-Signature: $signature",
        "X-Api-Ts: $timestamp",
        'Content-Type: multipart/form-data; boundary=boundary'
    ];

    $ch = curl_init($fullUrl);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $body);
    curl_setopt($ch, CURLOPT_POST, true);

    $response = curl_exec($ch);
    curl_close($ch);

    return $response;
}

// Example usage
$apiToken = 'your_api_token';
$secretKey = 'your_secret_key';
$filePath = 'path_to_your_file.pdf';
$response = uploadDocument($apiToken, $secretKey, 'applicant-id', $filePath);
echo $response;
```

### Summary

- Clients must include `X-Api-Token`, `X-Api-Signature`, and `X-Api-Ts` in every request.
- For **non-multipart** requests (e.g., creating a personal application), the JSON body is included in the signature.
- For **multipart** requests (e.g., uploading documents), both the form fields and file content are included in the signature.
- Requests must be sent over HTTPS to ensure security.

### Khalti PHP SDK

[

](https://packagist.org/packages/khalti/php-sdk)
[

](https://packagist.org/packages/khalti/php-sdk)
[

](https://github.com/prasangapokharel/khalti_sdk/blob/main/LICENSE)

A comprehensive PHP SDK for integrating Khalti Payment Gateway into your PHP applications.

## 📋 Table of Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)

- [E-Payment API](#e-payment-api)
- [Wallet API](#wallet-api)
- [Transaction API](#transaction-api)



- [Error Handling](#error-handling)
- [Logging](#logging)
- [Test Credentials](#test-credentials)
- [Contributing](#contributing)
- [License](#license)


## 🚀 Installation

### Via Composer

```shellscript
composer require khalti/php-sdk
```

## ⚙️ Configuration

You can configure the SDK in two ways:

### 1. Using an array

```php
use KhaltiSDK\Khalti;

$khalti = new Khalti([
    'environment' => 'sandbox', // or 'live'
    'secretKey' => 'your_secret_key',
    'publicKey' => 'your_public_key',
    'enableLogging' => true,
    'logPath' => '/path/to/logs/khalti.log',
    'logLevel' => 'info'
]);
```

### 2. Using a configuration file

```php
use KhaltiSDK\Khalti;

$khalti = new Khalti('/path/to/config/khalti.php');
```

Example configuration file:

```php
return [
    'environment' => 'sandbox',
    'secretKey' => 'your_secret_key',
    'publicKey' => 'your_public_key',
    'enableLogging' => true,
    'logPath' => __DIR__ . '/../logs/khalti.log',
    'logLevel' => 'info'
];
```

## 🔍 Usage

### E-Payment API

#### Initiating a Payment

```php
try {
    $params = [
        'return_url' => 'https://example.com/khalti/verify',
        'website_url' => 'https://example.com',
        'amount' => 1000, // Amount in paisa (10 NPR)
        'purchase_order_id' => 'ORDER-123',
        'purchase_order_name' => 'Test Order',
        'customer_info' => [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'phone' => '9800000000'
        ]
    ];

    $response = $khalti->ePayment()->initiate($params);
    
    // Redirect user to payment URL
    header('Location: ' . $response['payment_url']);
    exit;
} catch (Exception $e) {
    // Handle error
    echo $e->getMessage();
}
```

#### Verifying a Payment

```php
try {
    // Get pidx from return URL query parameter
    $pidx = $_GET['pidx'];
    
    $response = $khalti->ePayment()->verify($pidx);
    
    // Process verification response
    if ($response['status'] === 'Completed') {
        // Payment successful, update your database
        // ...
        
        // Redirect to success page
        header('Location: /payment/success');
        exit;
    } else {
        // Payment failed or pending
        // ...
        
        // Redirect to failure page
        header('Location: /payment/failure');
        exit;
    }
} catch (Exception $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Wallet API

#### Verifying a Wallet Payment

```php
try {
    // Token received from client-side Khalti widget
    $token = $_POST['token'];
    $amount = $_POST['amount']; // Amount in paisa
    
    $response = $khalti->wallet()->verify($token, $amount);
    
    // Payment successful, update your database
    // ...
    
    echo json_encode(['success' => true]);
} catch (Exception $e) {
    // Handle error
    echo json_encode(['success' => false, 'message' => $e->getMessage()]);
}
```

### Transaction API

#### Listing Transactions

```php
try {
    $transactions = $khalti->transaction()->list([
        'page' => 1,
        'page_size' => 10
    ]);
    
    foreach ($transactions['records'] as $transaction) {
        echo $transaction['idx'] . ': ' . $transaction['amount'] / 100 . ' NPR<br>';
    }
} catch (Exception $e) {
    // Handle error
    echo $e->getMessage();
}
```

#### Getting Transaction Details

```php
try {
    $transaction = $khalti->transaction()->find('transaction_idx');
    
    echo 'Amount: ' . $transaction['amount'] / 100 . ' NPR<br>';
    echo 'Status: ' . $transaction['status'] . '<br>';
    echo 'Date: ' . $transaction['created_on'] . '<br>';
} catch (Exception $e) {
    // Handle error
    echo $e->getMessage();
}
```

## 🛡️ Error Handling

The SDK throws different types of exceptions for different error scenarios:

- `ValidationException`: Thrown when request parameters are invalid
- `ApiException`: Thrown when the Khalti API returns an error
- `NetworkException`: Thrown when a network error occurs
- `ConfigurationException`: Thrown when the SDK configuration is invalid


Example error handling:

```php
use KhaltiSDK\Exceptions\ValidationException;
use KhaltiSDK\Exceptions\ApiException;
use KhaltiSDK\Exceptions\NetworkException;
use KhaltiSDK\Exceptions\ConfigurationException;

try {
    $response = $khalti->ePayment()->initiate($params);
    // Process response
} catch (ValidationException $e) {
    // Handle validation error
    echo "Validation Error: " . $e->getMessage();
} catch (ApiException $e) {
    // Handle API error
    echo "API Error (" . $e->getCode() . "): " . $e->getMessage();
    $responseData = $e->getResponseData(); // Get full response data
} catch (NetworkException $e) {
    // Handle network error
    echo "Network Error: " . $e->getMessage();
} catch (ConfigurationException $e) {
    // Handle configuration error
    echo "Configuration Error: " . $e->getMessage();
} catch (Exception $e) {
    // Handle other errors
    echo "Error: " . $e->getMessage();
}
```

## 📝 Logging

The SDK provides built-in logging functionality. You can enable it by setting `enableLogging` to `true` in the configuration.

Log levels:

- `debug`: Detailed debug information
- `info`: Interesting events
- `warning`: Exceptional occurrences that are not errors
- `error`: Runtime errors that do not require immediate action
- `critical`: Critical conditions


## 🧪 Test Credentials

Use these credentials for testing in the sandbox environment:

| Credential | Value
|-----|-----
| Test Phone Number | `9800000001`
| Test MPIN | `1111`
| Test OTP | `987654`
| Secret Key | `live_secret_key_68791341fdd94846a146f0457ff7b455`


## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request


## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 📞 Support

If you encounter any issues or have questions, please create an issue on GitHub or contact [support@khalti.com](mailto:support@khalti.com).
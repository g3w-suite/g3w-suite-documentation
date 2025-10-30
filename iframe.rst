============================
How to use G3W-SUITE in iframe
============================

To use G3W-SUITE within an iframe, you need to follow some fundamental steps:

1. **Create an iframe**: Include an iframe in your HTML code where you want to display G3W-SUITE.

.. code-block:: html

    <iframe src="YOUR_G3W_SUITE_URL" width="100%" height="600px"></iframe>

2. **Configure dimensions**: Make sure the iframe has adequate dimensions to properly display the G3W-SUITE application.

3. **Handle communications**: If you need to communicate between the iframe and the main page, use the postMessage API to send and receive messages.

4. **Security considerations**: Check browser and server security settings to ensure the iframe can load G3W-SUITE without issues.

By following these steps, you can effectively integrate G3W-SUITE within an iframe.

JWT Authentication for protected services
==========================================

It is possible to embed webgis services in an iframe that have free access or are protected by username and password.
For example, to embed a webgis service protected by authentication, it is possible to use a custom JWT authentication: 
the workflow involves obtaining a JWT authentication token and then using it within the iframe URL.

Below is an example of a PHP script that performs login and retrieves the JWT token to then insert it into the iframe URL:

.. code-block:: php

    <?php
    $apiUrl  = getenv("API_URL") ?: "http://localhost:8042/api/token/";
    $mapUrl  = getenv("MAP_URL") ?: "http://localhost:8042/en/map/g3w-suite-demo/qdjango/336/";
    $username = getenv("API_USER") ?: "viewer";
    $password = getenv("API_PASS") ?: "****************";

    // 1. Retrieve JWT token with POST JSON
    $ch = curl_init($apiUrl);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, ["Content-Type: application/json"]);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
         "username" => $username,
         "password" => $password
    ]));

    // Disable SSL verification (for testing only!)
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);

    $response = curl_exec($ch);
    if (curl_errno($ch)) {
         die("cURL request error: " . curl_error($ch));
    }
    curl_close($ch);

    // Decode JSON response
    $data = json_decode($response, true);
    if (!$data) {
         die("Invalid response from API: " . htmlspecialchars($response));
    }

    // Check token field name
    $token = $data['token'] ?? $data['access'] ?? null;
    if (!$token) {
         die("Token not found in response: " . htmlspecialchars($response));
    }

    // 2. Build URL with token
    $url = $mapUrl . "?token=Bearer " . urlencode($token);
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <title>Iframe with JWT</title>
      <style>
         iframe {
            width: 100%;
            height: 90vh;
            border: none;
         }
      </style>
    </head>
    <body>
      <iframe src="<?= htmlspecialchars($url) ?>"></iframe>
    </body>
    </html>

How the script works
====================

The script authenticates with an API to obtain a JWT token, then uses that token to display a protected map interface in an iframe.

Main URLs and flow
------------------

1. **API authentication URL**

.. code-block:: php

    $apiUrl = getenv("API_URL") ?: "http://localhost:8042/api/token/";

- **Purpose**: Endpoint for JWT token authentication
- **Default**: http://localhost:8042/api/token/
- **Method**: POST with JSON credentials

2. **Map application URL**

.. code-block:: php

    $mapUrl = getenv("MAP_URL") ?: "http://localhost:8042/en/map/g3w-suite-demo/qdjango/336/";

- **Purpose**: The protected map interface to embed
- **Default**: Points to a G3W-Suite demo map (project ID 336)
- **Final URL**: The token is added as a query parameter

Authentication process
-----------------------

1. **Token request**: Makes a POST request to the API with username/password
2. **Token extraction**: Handles different response formats (token or access field)
3. **URL construction**: Adds the token as ``?token=Bearer {token}``
4. **Iframe embedding**: Displays the authenticated map

Main features
-------------

- **Environment variables**: Uses ``getenv()`` for configuration flexibility
- **SSL bypass**: Disables SSL verification (⚠️ testing only)
- **Error handling**: Checks cURL errors and invalid responses
- **Security**: Uses ``htmlspecialchars()`` and ``urlencode()`` to prevent XSS

Final URL example
-----------------

.. code-block:: text

    http://localhost:8042/en/map/g3w-suite-demo/qdjango/336/?token=Bearer eyJ0eXAiOiJKV1QiLCJhbGc...

.. warning::
    **Security notes**
    
    - SSL verification bypass should never be used in production
    - Consider using POST requests for token transmission instead of GET parameters
    - Make sure to properly handle token expiration

This approach is designed for integration with G3W-Suite (WebGIS platform) that requires JWT authentication for map access.

G3W-SUITE configuration for JWT and iframe
===========================================

Below are the settings to configure in G3W-SUITE to ensure proper JWT authentication functionality and iframe usage:

Cookie configuration
--------------------

Inside the `local_settings.py` (or `settings_docker.py` if using Docker deployment), add or modify the following lines:

.. code-block:: python

    # Cookie configuration for JWT
    SESSION_COOKIE_SAMESITE = None
    CSRF_COOKIE_SAMESITE = None
    SESSION_COOKIE_SECURE = True
    CSRF_COOKIE_SECURE = True

These settings allow session and CSRF cookies to be used in an iframe context, while ensuring security through HTTPS.

.. warning::
    **Security notes**
    Important: the ``SAMESITE=None`` setting is accepted by browsers only if the protocol is HTTPS.

CORS response configuration
---------------------------
 Add the domain (or domains) from which you plan to load G3W-SUITE in an iframe to the following configuration:

.. code-block:: python

    CORS_ALLOWED_ORIGINS = [
        "https://your-domain.com",
        "https://another-domain.com",
    ]


X-Frame-Options directive configuration
----------------------------------------

It is recommended to configure the `X-Frame-Options` directive to allow G3W-SUITE to load in iframe only from authorized domains, within the reverse proxy configuration files in front of G3W-SUITE.
Below is an example configuration for Nginx:

.. code-block:: nginx

    add_header X-Frame-Options "ALLOW-FROM https://your-domain.com";

This directive is now deprecated so it is recommended to use the `Content-Security-Policy` header with the `frame-ancestors` directive for more granular control.
Also remove the `X-Frame-Options` directive if present.

.. code-block:: nginx

    proxy_hide_header X-Frame-Options;
    add_header Content-Security-Policy "frame-ancestors 'self' https://your-domain.com";


# Example Code for JSON Output in CodeIgniter 4 Controller

This example demonstrates how to perform a **DKIM DNS lookup** in a CodeIgniter 4 controller and return the result in JSON format.

## Code Example

```php
private function dkimLookup($target)
{
    try {
        // Validate whether the target is an IP Address
        if (filter_var($target, FILTER_VALIDATE_IP)) {
            return 'DKIM lookup is only available for domains, not IP addresses.';
        }

        // Remove 'www.' prefix if present
        $target = preg_replace('/^www\./i', '', $target);

        // List of DKIM selectors to check
        $selectors = ['default', 'selector1', 'selector2', 'mail', 'google', 'k1', 'smtpapi', 's1', 'protonmail', 'zoho', 'zmail', 'selector', 's2048', 's1024', 'krs', 'smtp', 'mx', 'dkim', 'email', 'mailchimp', 'news', 'mailgun', 'amazonses', 'selector3', 'selector4', 'selector5', 'postmark', 'pm', 's', 's2', 's3', 's4', 's5', 'keys', 'dk', 'mx1', 'mx2', 'mx3', 'mx4', 'cloud', 'key1', 'key2', 'key3', 'selector6', 'selector7', 'selector8', 'selector9', 'selector10', 'google1', 'google2', 'sig1', 'sig2', 'fm1', 'fm2', 'tutanota', 'hushmail', 'gmx', 'mailbox', 'mailfence', 'posteo', 'skiff'];
        $status = null;

        foreach ($selectors as $selector) {
            $command = "host -t txt " . escapeshellarg($selector . "._domainkey." . $target);
            $tempOutput = [];
            exec($command, $tempOutput, $status);

            if ($status === 0) {
                $found = array_filter($tempOutput, function ($line) {
                    return stripos($line, 'v=DKIM') !== false;
                });

                if (!empty($found)) {
                    return implode("\n", $found);
                }
            }
        }

        return 'No DKIM data was found for this domain.';
    } catch (\Exception $e) {
        return 'There is an error: ' . $e->getMessage();
    }
}
```

## How It Works
1. **Input Validation**: The code checks if the input is an IP address — DKIM lookups only work for domains.
2. **Domain Cleanup**: It removes `www.` from the domain if present.
3. **Selector Iteration**: The method iterates over an array of selectors including popular selectors from various **Email Service Providers**.
4. **DNS Lookup Execution**: Executes the `host` command to query TXT records for each selector.
5. **Result Filtering**: Filters the output to check if the result contains `v=DKIM`.
6. **Output**: Returns the DKIM record if found or an appropriate message if not.

## Supported Email Providers
- Gmail
- Outlook
- Yahoo! Mail
- iCloud Mail
- Zoho Mail
- ProtonMail
- Mailchimp
- Mailgun
- Amazon SES
- Postmark
- SendGrid
- Yandex Mail
- Fastmail
- Tutanota
- Hushmail
- GMX Mail
- AOL Mail
- Mail.com
- Mailbox.org
- Mailfence
- Posteo
- Skiff

## Common Selectors
- `default`
- `k1`
- `s1`
- `s2`
- `s2048`
- `s1024`
- `dkim`
- `smtp`
- `keys`
- `mx`
- `email`
- `cloud`
- `mail`
- `smtpapi`
- `pm`
- `key1`
- `key2`
- `key3`

## Example Usage
```php
$domain = 'example.com';
echo $this->dkimLookup($domain);
```

## Notes
- Make sure the `host` command is available on your server.
- The selector list is continuously updated to cover more email providers.
- Customize the selector array to add additional selectors if needed.

## License
This project is licensed under the MIT License.


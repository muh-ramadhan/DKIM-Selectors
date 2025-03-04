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
        $selectors = ['default', 'selector1', 'selector2', 'mail', 'google', 'k1', 'smtpapi', 's1', 'protonmail', 'zoho', 'zmail', 'selector', 's2048', 's1024', 'krs', 'smtp', 'mx', 'dkim', 'email', 'mailchimp', 'news', 'mailgun', 'amazonses', 'selector3', 'selector4', 'selector5', 'postmark', 'pm', 's', 's2', 's3', 's4', 's5', 'keys', 'dk', 'mx1', 'mx2', 'mx3', 'mx4', 'cloud', 'key1', 'key2', 'key3', 'selector6', 'selector7', 'selector8', 'selector9', 'selector10'];
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
3. **Selector Iteration**: The method iterates over an array of selectors (`default`, and custom selectors).
4. **DNS Lookup Execution**: Executes the `host` command to query TXT records for each selector.
5. **Result Filtering**: Filters the output to check if the result contains `v=DKIM`.
6. **Output**: Returns the DKIM record if found or an appropriate message if not.

## List of Supported DKIM Selectors
This code supports DKIM selectors from various email providers, including but not limited to:

- **Google**: `google`
- **Microsoft Outlook**: `selector1`, `selector2`
- **ProtonMail**: `protonmail`
- **Zoho Mail**: `zoho`, `zmail`
- **Mailchimp**: `k1`
- **Mailgun**: `mailgun`
- **Amazon SES**: `amazonses`
- **Postmark**: `postmark`, `pm`
- **SendGrid**: `smtpapi`

## Example Usage
```php
$domain = 'example.com';
echo $this->dkimLookup($domain);
```

## Notes
- Make sure the `host` command is available on your server.
- Customize the `INSERT_LIST_HERE` array to add additional selectors.

## License
This project is licensed under the MIT License.


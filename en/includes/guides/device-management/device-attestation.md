# Device attestation

Attestation is how {{product_name}} learns whether a device is genuine rather than an emulator, a modified build, or a repackaged copy of your application. Google and Apple each provide an attestation service, and {{product_name}} turns their verdict into a [device attribute]({{base_path}}/guides/device-management/device-attributes/) that you can write policy conditions against.

## Configure attestation

Attestation is configured per application, on the **Advanced** tab of the application under the **Client Attestation** section. For the full steps, including the service account credentials needed for Android and the app ID needed for Apple, see [Secure app-native authentication flows]({{base_path}}/guides/authentication/app-native-authentication/secure-app-native-authentication-flows/#using-client-attestation).

!!! note
    Until attestation is configured for an application, the `Android integrity` and `iOS device genuine` attributes carry no useful verdict. A policy that depends on them will not behave as you expect.

## Android integrity levels

Google returns a verdict, and {{product_name}} maps it to one of the following values, strongest first.

<table>
    <thead>
        <tr>
            <th>Value</th>
            <th>What Google is saying</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>MEETS_STRONG_INTEGRITY</code></td>
            <td>Genuine device, recent security updates, and strong hardware-backed guarantees.</td>
        </tr>
        <tr>
            <td><code>MEETS_DEVICE_INTEGRITY</code></td>
            <td>Genuine Android device running genuine Android software.</td>
        </tr>
        <tr>
            <td><code>MEETS_BASIC_INTEGRITY</code></td>
            <td>The device passes basic checks, but Google cannot fully vouch for it.</td>
        </tr>
        <tr>
            <td><code>MEETS_VIRTUAL_INTEGRITY</code></td>
            <td>An emulator running genuine Android software, rather than a physical device.</td>
        </tr>
        <tr>
            <td><code>INTEGRITY_FAILED</code></td>
            <td>Google could not verify the device, or attestation is not working.</td>
        </tr>
    </tbody>
</table>

When Google returns more than one verdict, {{product_name}} keeps the strongest one, so a device that meets strong integrity always reports `MEETS_STRONG_INTEGRITY`.

The **Android integrity** attribute supports only the `equals` and `not equals` operators, so a rule cannot say "this level or stronger" directly. To accept genuine devices only, exclude the weaker values rather than merely the failed one. For example, the following conditions accept `MEETS_STRONG_INTEGRITY` and `MEETS_DEVICE_INTEGRITY`:

```text
androidIntegrity not equals MEETS_BASIC_INTEGRITY
AND androidIntegrity not equals MEETS_VIRTUAL_INTEGRITY
AND androidIntegrity not equals INTEGRITY_FAILED
```

## iOS device genuine

Attestation from Apple is a simpler yes-or-no verdict. Either the device and the application passed verification (`true`) or they did not (`false`).

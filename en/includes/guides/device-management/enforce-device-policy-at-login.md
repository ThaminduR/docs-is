# Enforce a device policy at login

Registration decides whether a device is allowed to be registered. Login decides whether a device is allowed in today. These are separate checks on purpose. A phone that was compliant when it registered six months ago may have been rooted since, or may have fallen behind on operating system updates.

Login enforcement is done through a [conditional authentication]({{base_path}}/guides/authentication/conditional-auth/) script on the sign-in flow of the application. The script has access to the data reported by the device and can decide what to do with it: allow the sign-in, block it, or step up to an additional factor.

## Enforcement patterns

Because the decision sits in a script, you can be more nuanced than a simple pass or fail. The following patterns are common:

- **Hard block** - The device is non-compliant, so refuse the sign-in and tell the user what to fix.

- **Step up** - The device is non-compliant, so require an additional factor rather than refusing outright.

- **Sensitive applications only** - Enforce the policy on the finance application but not on the cafeteria menu application.

- **Grace period** - Warn users for a period before enforcing, so they have time to bring devices into line.

## Send the device token

At login, the application proves which device is signing in by presenting a device token. The device token is a short-lived JSON Web Signature (JWS) that the application creates and signs on the device:

- It is signed with the `ES256` algorithm, using the private key that the device generated when it was [registered]({{base_path}}/guides/device-management/configure-device-registration/).

- Its JOSE header carries the `deviceId` of the registered device, so that {{product_name}} can look up the matching public key.

- Its claims must include a unique `jti` and an `iat` (issued at) time. {{product_name}} accepts a token only if it was issued within about the last five minutes, and accepts each token only once. Create a new token for every sign-in attempt.

- Its claims carry the device data that the policy is evaluated against, keyed by the field names of the [device attributes]({{base_path}}/guides/device-management/device-attributes/), such as `platform` and `isRooted`.

The application sends the token on the authorization request that starts the sign-in. With [app-native authentication]({{base_path}}/guides/authentication/app-native-authentication/), send it in the `X-Device-Token` header. When the application starts the sign-in with a redirect instead, send it in the `device_token` query parameter of the authorization request.

{{product_name}} verifies the signature against the registered public key of the device and uses the verified data for the policy evaluation. A token from a device that is inactive or no longer registered fails verification.

## Add the script

To enforce a policy on the login flow of an application:

1. On the {{product_name}} Console, go to **Applications**.

2. Select the relevant application and go to its **Login Flow** tab.

3. Add the following script, replacing `android-baseline` with the name of your [device assurance policy]({{base_path}}/guides/device-management/device-assurance-policies/).

    ```js
    var onLoginRequest = function(context) {
        executeStep(1, {
            onSuccess: function(context) {
                var reason = isDevicePolicyCompliant(context, 'android-baseline');
                if (reason) {
                    fail({'errorCode': 'device_policy', 'errorMessage': reason});
                }
            }
        });
    };
    ```

4. Click **Update** to confirm.

## How it works

`isDevicePolicyCompliant(context, policyName)` evaluates the named policy against the verified device data from the device token that the application presented at login.

When the device satisfies every condition in the rule for its platform, the function returns nothing (`null`) and the script lets the sign-in proceed. Otherwise, it returns a string that explains why the check did not pass:

<table>
    <thead>
        <tr>
            <th>Returned value</th>
            <th>Meaning</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Nothing (<code>null</code>)</td>
            <td>The device complies with the policy.</td>
        </tr>
        <tr>
            <td>Comma-separated field names, such as <code>isRooted, usbDebugging</code></td>
            <td>The device is non-compliant. The fields are the <a href="{{base_path}}/guides/device-management/device-attributes/">field names</a> of the conditions that failed.</td>
        </tr>
        <tr>
            <td>Comma-separated field names, such as <code>platform</code></td>
            <td>The device data is incomplete. The fields are the ones the policy needs but the device did not report.</td>
        </tr>
        <tr>
            <td><code>&lt;policyName&gt;:device token is missing or validation failed</code></td>
            <td>The request has no device token, or the token could not be verified. For example, the token was already used, has expired, or belongs to a device that is inactive or not registered.</td>
        </tr>
        <tr>
            <td><code>&lt;policyName&gt;:device_not_owned</code></td>
            <td>The device is registered to a different user than the one who has authenticated so far in the flow.</td>
        </tr>
        <tr>
            <td><code>&lt;policyName&gt;:policy_not_found</code></td>
            <td>No policy with the given name exists in the organization.</td>
        </tr>
        <tr>
            <td><code>&lt;policyName&gt;:policy_error</code></td>
            <td>The policy could not be evaluated because of a problem with the policy.</td>
        </tr>
        <tr>
            <td><code>&lt;policyName&gt;:evaluation_error</code></td>
            <td>An unexpected error occurred while evaluating the policy.</td>
        </tr>
    </tbody>
</table>

The example above passes the returned value into the error message, so the user is told what to fix rather than being refused without explanation. To step up instead of blocking, call `executeStep` for an additional factor in place of `fail`.

!!! note "Call the function after the user is identified"
    The device ownership check runs only once a user has been identified in the flow. Call `isDevicePolicyCompliant` in the `onSuccess` callback of the first step, as in the example above, so that a device registered to another user is rejected with `device_not_owned`. If you call it before any step completes, the device posture is still checked, but not who owns the device.

!!! note
    The policy name in the script must match the name of the policy exactly. Since a policy name cannot be changed after creation, the reference stays valid once you have set it.

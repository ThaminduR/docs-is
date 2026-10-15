# Configure device registration

Before {{product_name}} can assert anything about a device, the device has to be registered. Registration links a device to a user account and establishes the credential the device uses to identify itself afterwards.

## A native application is required

Registration requires the device to generate a cryptographic key pair, hold the private key in the secure hardware of the device, and sign a challenge with it. Only a native mobile or desktop application can do that. A browser has no access to the key store of the device, so device registration cannot be offered to users who only use your application in a browser.

Your native application runs the registration flows through the [Flow Execution API]({{base_path}}/guides/flows/flow-execution-api/). When the flow reaches the device registration step, {{product_name}} returns a challenge. The application signs the challenge with the private key of the device and sends back the signature together with the public key, the device name and model, and the device data that the policy needs. {{product_name}} verifies the signature before it stores the device.

To sign in with the registered device later, the application presents a device token signed with the same key. See [Enforce a device policy at login]({{base_path}}/guides/device-management/enforce-device-policy-at-login/#send-the-device-token).

## Registration flows

A device can be registered through three different flows, all of which you build in the [flow builder]({{base_path}}/guides/flows/build-a-flow/) of the {{product_name}} Console. Which one you use depends on when in the lifecycle of the user you want the device enrolled.

### Self-registration

The user signs themselves up and registers their device as part of creating their account. Registration happens at the same moment the account is created, so the user has a trusted device from their first sign-in.

Use this when users onboard themselves through a public sign-up page. To enable it, add the **Device Registration** step to the [self-registration flow]({{base_path}}/guides/flows/self-registration/).

### Invited user registration

An administrator invites the user, the user accepts the invitation, and the device is registered as part of accepting it. The account already exists, and the invitation flow completes the setup, including enrolling the device.

Use this when access is granted by an administrator rather than requested by the user, which is the usual pattern inside an organization. To enable it, add the **Device Registration** step to the [invited user registration flow]({{base_path}}/guides/flows/invited-user-registration/).

### Dedicated device registration flow

A standalone flow whose only purpose is registering a device against an account that already exists. The user identifies themselves with their username, proves control of the account with a one-time passcode, and then registers the device.

Use this for the cases the other two flows do not cover, which in practice is most of the ongoing work:

- A user who already had an account before device assurance was introduced.

- A user who has replaced their phone.

- A user who needs a second device, such as a tablet alongside a phone, or a work laptop alongside both.

Most organizations end up using this flow far more than the other two once the initial rollout is done.

!!! warning "An OTP step is required"
    A dedicated device registration flow must include an **Email OTP** or **SMS OTP** step. Without one, anyone who knows a username could bind their own device to that account. {{product_name}} rejects a device registration flow that does not contain one of these steps.

To set up the dedicated flow:

1. On the {{product_name}} Console, go to **Flows**.

2. Click the **Device Registration** card to open the flow builder.

3. Under **Starter Templates**, click **+** next to one of the following templates to load it onto the canvas:

    - **Device Registration + Email OTP** - The user enters their username and verifies a one-time passcode sent to their email address.

    - **Device Registration + SMS OTP** - The user enters their username and verifies a one-time passcode sent to their mobile number.

    - **Blank** - Build the flow from scratch. Remember to add an Email OTP or SMS OTP step and the **Device Registration** step.

    ![Device registration flow in the flow builder]({{base_path}}/assets/img/guides/device-management/device-registration-flow-builder.png){: width="800" style="display: block; margin: 0; border: 0.3px solid lightgrey;"}

4. Optionally, [attach a device assurance policy](#attach-a-policy-to-registration) to the **Device Registration** step.

5. Click **Publish** and make sure the flow is enabled.

Your application starts the flow by calling the [Flow Execution API]({{base_path}}/guides/flows/flow-execution-api/) with `DEVICE_REGISTRATION` as the `flowType`.

## Attach a policy to registration

Any of the three flows can optionally enforce a [device assurance policy]({{base_path}}/guides/device-management/device-assurance-policies/) at registration time. Select the **Device Registration** step in the flow builder and choose a policy under **Device assurance policy**. Non-compliant devices are then refused at the point of registration rather than allowed in and blocked later.

If you select **No policy (skip check)**, the device is registered without any posture check. The device is identified, but nothing is asserted about how secure it is.

!!! tip
    Enforcing at registration is worth doing even when you also [enforce at login]({{base_path}}/guides/device-management/enforce-device-policy-at-login/). It gives the user a clear failure at a moment when they are already setting something up and expecting to follow instructions, rather than at the start of a working day when they are trying to get to work.

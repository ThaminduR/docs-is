# Device assurance policies

A device assurance policy is a named set of conditions that a device must meet. You define the policy once and then reference it by name in two places: at [registration time]({{base_path}}/guides/device-management/configure-device-registration/) and at [login time]({{base_path}}/guides/device-management/enforce-device-policy-at-login/).

A policy is made up of one rule per platform. You do not write a single rule that covers every operating system. Instead, you write an Android rule, an iOS rule, a macOS rule, and a Windows rule, and {{product_name}} evaluates each one only against devices of that platform.

This design exists because the security attributes of an Android phone and a Windows laptop do not overlap. Asking a Windows machine whether it is jailbroken is meaningless.

## How a policy is structured

The following example shows a policy named `Corporate Device Policy` with a rule for each of the four platforms.

```text
Policy: "Corporate Device Policy"
 ├── android → rule: isRooted equals false AND lockScreen equals true AND diskEncryption equals true
 ├── ios     → rule: jailbreak equals false AND passcode equals true
 ├── macos   → rule: diskEncryption equals true AND secureEnclave equals true
 └── windows → rule: trustedPlatformModule equals true AND windowsHello equals true
```

When a device presents itself, {{product_name}} reads its `platform` attribute, picks the matching rule, and evaluates only that rule. If the device reports a platform that has no rule, no condition applies to it and the device passes. If the device does not report a platform at all, the policy cannot be evaluated and the device does not pass.

!!! warning
    Leaving a platform out of a policy means devices on that platform are never checked. Add a rule for every platform you intend to enforce.

## Create a policy

Each policy needs the following:

<table>
    <thead>
        <tr>
            <th>Field</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><b>Policy Name</b></td>
            <td>A name between 3 and 80 characters that is unique within the organization. This is the name you reference from the registration flow and from the conditional authentication script, so pick something stable. The name cannot be changed after you create the policy.</td>
        </tr>
        <tr>
            <td><b>Target platforms</b></td>
            <td>The platforms the policy covers. Select one or more of <b>Android</b>, <b>iOS</b>, <b>macOS</b>, and <b>Windows</b>.</td>
        </tr>
        <tr>
            <td><b>Execution rules</b></td>
            <td>One rule for each target platform. A rule is a set of conditions, and a device passes the rule only if it meets all of them. Each condition compares a <a href="{{base_path}}/guides/device-management/device-attributes/">device attribute</a> with a value.</td>
        </tr>
    </tbody>
</table>

To create a policy:

1. On the {{product_name}} Console, go to **Login & Registration**.

2. Under **Policies**, click **Device Assurance**. The **Device Assurance Policies** page lists the existing policies.

    ![Device assurance policies]({{base_path}}/assets/img/guides/device-management/device-assurance-policies-list.png){: width="800" style="display: block; margin: 0; border: 0.3px solid lightgrey;"}

3. Click **+ Create Device Assurance Policy**.

4. In the **Basic Details** step, enter a **Policy Name**, select the **Target platforms**, and click **Next**.

    ![Create a device assurance policy]({{base_path}}/assets/img/guides/device-management/create-device-assurance-policy.png){: width="600" style="display: block; margin: 0; border: 0.3px solid lightgrey;"}

5. In the **Execution Rules** step, configure the rule for each platform:

    1. Select the tab of the platform.

    2. Click **Configure Rule**.

    3. Add the conditions the device must meet. For each condition, select an attribute, an operator, and a value. The attributes offered depend on the platform.

    ![Configure the execution rule for each platform]({{base_path}}/assets/img/guides/device-management/device-assurance-policy-rules.png){: width="600" style="display: block; margin: 0; border: 0.3px solid lightgrey;"}

    Then, click **Next**.

    !!! warning
        If you do not configure a rule for a target platform, the policy applies no conditions to devices on that platform, and every such device passes.

6. In the **Review & Save** step, review the policy and save it.

## View or edit a policy

To view a policy, click it on the **Device Assurance Policies** page. The policy page shows a tab for each target platform, with the condition, operator, and value of every condition in that rule.

![View a device assurance policy]({{base_path}}/assets/img/guides/device-management/device-assurance-policy-view.png){: width="800" style="display: block; margin: 0; border: 0.3px solid lightgrey;"}

To change the rules, click **Edit Policy**. To delete a policy, click the delete icon next to it on the **Device Assurance Policies** page.

!!! note
    Before you delete a policy, remove it from any registration flow and conditional authentication script that references it. A script that references a deleted policy receives `policy_not_found` from [isDevicePolicyCompliant]({{base_path}}/guides/device-management/enforce-device-policy-at-login/#how-it-works).

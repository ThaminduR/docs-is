# Manage registered devices

When a user [registers a device]({{base_path}}/guides/device-management/configure-device-registration/) with {{product_name}}, the device is linked to their account and can be used to sign in. Users can review and manage these devices from the My Account portal.

This guide explains how users can rename, deactivate, and remove their registered devices.

## View registered devices

1. In the user's My Account portal, go to **Security**.

2. Scroll down to the **Registered devices** section. It lists each device registered to the account, with its name, model, status, and when it was registered.

    ![Registered devices in My Account]({{base_path}}/assets/img/guides/organization/self-service/myaccount/registered-devices.png){: width="800" style="display: block; margin: 0; border: 0.3px solid lightgrey;"}

!!! note
    If no devices are registered to the account, the section shows that there are no registered devices. Devices are registered from applications, not from My Account.

## Rename a device

1. In the **Registered devices** section, click the edit icon next to the device.

2. In the **Rename device** dialog, enter a new **Device name**.

    ![Rename a device]({{base_path}}/assets/img/guides/organization/self-service/myaccount/rename-device.png){: width="400" style="display: block; margin: 0; border: 0.3px solid lightgrey;"}

3. Click **Update**.

## Deactivate or activate a device

Deactivate a device that you want to stop using for a while, for example, a device that you have misplaced but expect to find again. An inactive device cannot be used to sign in.

1. In the **Registered devices** section, click the deactivate icon next to the device. The status of the device changes to **Inactive**.

2. To use the device again, click the activate icon next to it. The status changes back to **Active**.

## Remove a device

Remove a device that you no longer use or do not recognize.

1. In the **Registered devices** section, click the delete icon next to the device.

2. In the confirmation dialog, select **Please confirm your action.** and click **Remove**.

    ![Remove a device]({{base_path}}/assets/img/guides/organization/self-service/myaccount/remove-device.png){: width="500" style="display: block; margin: 0; border: 0.3px solid lightgrey;"}

!!! warning
    A removed device cannot be used to sign in. To use it again, you need to register the device again.

!!! info "Build your own device management experience"
    The My Account portal manages devices through the `/api/users/v1/me/devices` REST API. Your own application can use the same API with the access token of the signed-in user to list devices (`GET`), view a device (`GET /{device-id}`), rename or change the status of a device (`PATCH /{device-id}` with `deviceName` or `status`), and remove a device (`DELETE /{device-id}`). Users can only access the devices registered to their own account.

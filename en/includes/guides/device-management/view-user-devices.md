# View the devices of a user

As an administrator, you can review and manage the devices registered against a user account from the {{product_name}} Console.

1. On the {{product_name}} Console, go to **User Management** > **Users**.

2. Select the user whose devices you want to review.

3. Go to the **Devices** tab.

    ![Devices of a user]({{base_path}}/assets/img/guides/device-management/user-devices-tab.png){: width="800" style="display: block; margin: 0; border: 0.3px solid lightgrey;"}

The tab lists the devices currently registered to that user, with the name and model of each device, its status, and when it was registered. Use it to confirm that a user has enrolled a device, or to see how many devices a user has registered before asking them to enroll another one through the [dedicated device registration flow]({{base_path}}/guides/device-management/configure-device-registration/#dedicated-device-registration-flow).

An **Active** device can be used to sign in. An **Inactive** device has been deactivated, for example by the user from the My Account portal, and its device tokens are rejected at login until the user activates it again.

## Rename a device

To give a device a clearer name, click the edit (pencil) icon next to it, enter the new name, and save it.

## Remove a device

If a device is lost, replaced, or no longer trusted, click the delete (trash) icon next to it and confirm. A removed device can no longer be used to sign in. To use it again, the user must register it again.

!!! tip
    Users can also review, rename, deactivate, and remove their own devices from the My Account portal. See [Manage registered devices]({{base_path}}/guides/user-self-service/manage-registered-devices/).

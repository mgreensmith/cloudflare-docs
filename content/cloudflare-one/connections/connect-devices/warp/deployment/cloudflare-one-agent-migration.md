---
pcx_content_type: how-to
title: Migrate to Cloudflare One Agent
weight: 12
---

# Migrate to the Cloudflare One Agent

Users can connect to Cloudflare Zero Trust services through an agent that runs on their device. Cloudflare previously bundled that functionality into the [WARP client](/warp-client/), an application that also provides privacy-focused DNS and VPN services for consumers (known as 1.1.1.1 w/ WARP). Supporting both enterprise and consumer functionality in the same application allowed us to build Zero Trust upon the same foundation used by millions of consumers across the globe, but has limited the pace at which changes could be released. As a result, we are launching a dedicated Cloudflare One Agent that replaces the Cloudflare WARP client for Zero Trust deployments.

The Cloudflare One Agent supports all existing Zero Trust functionality. The underlying connection technology remains the same, and improvements made to performance and reliability based on feedback from 1.1.1.1 w/ WARP users will continue to be built into the Cloudflare One Agent.

## macOS, Windows, and Linux

No action is required for desktop clients at this time. The existing Cloudflare WARP client will continue to support both Zero Trust and 1.1.1.1 w/ WARP functionality.

## iOS and Android

Zero Trust users must migrate from the 1.1.1.1 w/ WARP app to the Cloudflare One Agent app by 2023-09-30.

Organizations can migrate their teams with minimal disruption in one of two modes: [manually](#migrate-manual-deployments) or via a [managed endpoint solution](#migrate-managed-deployments).

### Migration impact

- New Zero Trust features will only be released to the Cloudflare One Agent.
- 1.1.1.1 w/ WARP will continue to support the current feature set (and any security updates) through 2023-09-30.
- After 2023-09-30, all Zero Trust functionality will be removed from 1.1.1.1 w/ WARP. Users will no longer be able to log in to your Zero Trust organization through 1.1.1.1 w/ WARP.

### Migrate manual deployments

If you downloaded and installed the WARP client manually, here are the recommended migration steps:

1. Update the **1.1.1.1** app to the latest version. The update ensures that 1.1.1.1 can [co-exist](#what-to-do-with-the-old-app) with the new Cloudflare One Agent app. If you do not wish to keep both apps, you can go ahead and [uninstall 1.1.1.1](/cloudflare-one/connections/connect-devices/warp/remove-warp/#ios-and-android).
2. [Download](/cloudflare-one/connections/connect-devices/warp/download-warp/#ios) the **Cloudflare One Agent** app.
3. Install the Cloudflare One Agent: {{<render file="_enroll-ios-android.md">}}
4. In the Cloudflare One Agent, turn on the switch to **Connected**.

If you enrolled the Cloudflare One Agent in the same Zero Trust organization as 1.1.1.1, you will be automatically logged out of Zero Trust on 1.1.1.1. The 1.1.1.1 app will revert to consumer mode, and the **Login with Cloudflare Zero Trust**  button on the old app will redirect to the new app.

If you enrolled the Cloudflare One Agent in a different Zero Trust organization, you will remain logged into your other Zero Trust organization on 1.1.1.1.

#### What to do with the old app

While both 1.1.1.1 and Cloudflare One Agent can exist on the device, iOS and Android will only allow one of these applications to connect at a time.

To access your company's resources, you must use the Cloudflare One Agent app.

You can use the 1.1.1.1 app for personal browsing. When connected to 1.1.1.1 w/ WARP, your traffic will be encrypted and privately routed via Cloudflare's network, and your employer will not be able to see any of your browsing activity. To learn more about consumer WARP services, refer to [WARP client](/warp-client/).

If you do not wish to use the old 1.1.1.1 app for personal browsing, you may [uninstall](/cloudflare-one/connections/connect-devices/warp/remove-warp/#ios-and-android) it.

### Migrate managed deployments

If you deployed the WARP client with an [MDM provider](/cloudflare-one/connections/connect-devices/warp/deployment/mdm-deployment/), perform the migration as follows:

1. Configure your MDM to uninstall the **1.1.1.1** application.

2. Update your MDM configuration with the new application ID: `com.cloudflare.cloudflareoneagent`

    The other [WARP deployment parameters](/cloudflare-one/connections/connect-devices/warp/deployment/mdm-deployment/parameters/) you have configured remain the same.

3. To minimize user confusion, we recommend simultaneously removing **1.1.1.1** and installing **Cloudflare One Agent**.

4. On Android, the user will need to [re-authenticate](/cloudflare-one/connections/connect-devices/warp/deployment/manual-deployment/#ios-android-and-chromeos) to the new application, following the same onboarding steps they went through initially.

    On iOS, the user does not need to re-authenticate — registration data from the 1.1.1.1 w/ WARP app is automatically migrated to the new Cloudflare One Agent app.

Once users have enrolled, the migration process is complete.

### Verify migration

To check whether a user has migrated, go to **My Team** > **Devices**. A device enrolled through the Cloudflare One Agent will appear as a new device with a new device ID. Their old WARP client registration will remain as an inactive device.
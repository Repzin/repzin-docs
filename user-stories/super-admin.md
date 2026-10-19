# Super Admin User Stories

1. As a super admin, I want to register new gyms in the system.
   - Require a unique gym name.
   - Assign each gym a unique, human-readable gym ID.

2. As a super admin, I want to configure each gym independently.
   - Apply configuration changes immediately.

3. As a super admin, I want to configure all gyms in bulk so I can enable or disable features when issues occur.
   - Bulk changes override existing configurations.

4. As a super admin, I want to block gyms with unpaid fees and restore access when they pay.
   - Clearly show each gym's blocked or unblocked status.
   - Provide a clear notification when a gym is blocked or unblocked.

5. As a super admin, I want to register ESP32 devices for a gym so I can connect access control terminals.
   - Assign each ESP32 device a unique ID.
   - Allow the system to identify all ESP32 devices associated with a gym ID.

6. As a super admin, I want to register biometric terminals under the appropriate gym and ESP32 device.
   - Display registered devices.
   - **To discuss:** Define the required terminal information.

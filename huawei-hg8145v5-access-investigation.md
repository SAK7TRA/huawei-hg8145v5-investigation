# Huawei HG8145V5 Web-Management Access Investigation

> **Scope:** Authorized investigation of a Huawei HG8145V5 GPON ONT used on a Zain connection. This write-up omits credentials, salts, device identifiers, network addresses, ACS details, and exploit instructions.

## Summary

The goal was to regain web-management access without performing a factory reset that could erase ISP provisioning and connectivity settings.

The investigation combined the web interface, local JavaScript assets, and an offline review of a downloaded configuration backup. The intended web-management access goal was ultimately achieved without beginning with a factory reset. The evidence also helped distinguish web-management accounts from WAN/PPPoE credentials.

This is a device-specific case study, not a universal recovery method for all Huawei ONTs or ISP firmware.

## Target

- **Model:** Huawei EchoLife HG8145V5
- **Hardware revision:** 15AD.A
- **Software version:** V5R020C10S254
- **Customization:** Zain

Unique identifiers and service secrets are intentionally excluded.

## Investigation

### 1. Review the login flow and local assets

The login flow and local JavaScript assets were reviewed to understand the interface's intended login and password-management endpoints. Client-side code alone did not establish a server-side recovery bypass.

The login page indicated that forgotten-password recovery could require a factory reset, with a warning that settings could be lost. A reset was treated as a last resort.

### 2. Separate credential contexts

The web-management account is separate from the WAN/PPPoE account shown in the WAN configuration. A WAN username and password should not be assumed to authenticate to the web interface.

Likewise, generic credentials documented for other Huawei configurations are not proof of the active credentials on a customized Zain firmware build.

### 3. Inspect the configuration backup offline

The XML backup contained two enabled web-account records:

- `root`
- `telecomadmin`

Both web-account password fields used opaque `PassMode="3"` values with salts; the values were not plaintext passwords. The backup therefore established account names and stored representations, but did not itself reveal either original password. A separate CLI account record also had an opaque password field.

No password cracking or guessing was performed as part of the documented analysis. The original backup was kept unchanged. Configuration backups should be treated as sensitive because they can contain Wi-Fi, WAN, TR-069, and other service secrets.

### 4. Correlate the backup with the web interface

Screenshots showed an authenticated `telecomadmin` session and an Account Management menu. This established that an authenticated session existed when the screenshots were captured. It did not, by itself, prove that the account-management page could reset another account without a restart or ISP reprovisioning.

WAN, ACS, and ONT-authentication pages were treated as sensitive configuration areas.

### 5. Preserve service configuration

The investigation ultimately reached the intended web-management access goal without starting with a factory reset. The key operational lesson is to preserve an existing authenticated session, collect local evidence, and avoid destructive recovery actions until their effects are understood.

This write-up does not claim that the password fields were decrypted, that a generic credential worked, or that a universal recovery technique was discovered. The exact final access mechanism should only be described more specifically if it is separately documented and verified.

## Findings

### Confirmed by the collected evidence

- The device identified itself as a Huawei HG8145V5, hardware revision 15AD.A, software version V5R020C10S254.
- The interface identified Zain customization.
- The backup contained enabled web-account records named `root` and `telecomadmin`.
- Both web-account password fields were opaque `PassMode="3"` values with salts, not readable plaintext.
- WAN credentials were separate from the web-management login context.
- Screenshots showed an authenticated `telecomadmin` session and an Account Management menu at capture time.
- Web-management access was ultimately achieved without starting with a factory reset.

### Not established by the backup alone

- The plaintext password for either account.
- Whether a third-party conversion or script works against this exact firmware build.
- Whether every HG8145V5, ISP profile, or firmware version behaves the same way.
- Whether every account-management operation is non-disruptive to ISP provisioning.

## Safety notes

- Do not publish configuration backups, password fields, salts, Wi-Fi keys, PPPoE credentials, ACS URLs or passwords, serial numbers, GPON identifiers, or screenshots containing them.
- Do not restore a configuration from another ONT or ISP profile.
- Do not change VLAN, WAN, GPON authentication, TR-069, or firmware settings during routine password recovery.
- Avoid factory reset unless the ISP confirms the required provisioning details can be restored.
- Before applying account changes, read the warnings and confirm with the ISP whether the operation could restart the ONT or trigger reprovisioning.

## Limitations

This is a case study of one ISP-customized device and one firmware build. Huawei ONT web interfaces and account behavior can vary by model, firmware, and ISP customization. Findings should not be generalized without authorized testing.

## Conclusion

The useful result was not a universal password or a proven decryption method. It was an evidence-led path to the access goal: identify the exact device build, distinguish web credentials from WAN credentials, inspect the backup offline, preserve the existing authenticated session, and avoid destructive changes while service configuration is at risk.

Use this process only on equipment you own or are explicitly authorized to administer.

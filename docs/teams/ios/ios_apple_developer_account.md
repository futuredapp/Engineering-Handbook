# Apple Developer Organization Account Setup

This page describes how to create and set up a new Apple Developer organization account for releases under the client's organization.

## Existing Apple Developer Organization Account

If the client already has an Apple Developer organization account:

1. Do not create a new membership.
2. Continue with [Customer Account](ios_customer_account.md) to grant Futured access and finish the operational setup.

## New Apple Developer Organization Account

This guide covers the `Apple Developer Program` organization enrollment flow.

!!! danger "This step has to be done by the client - the future Account Holder."

    The person who completes the enrollment becomes the `Account Holder`.

    This role is responsible for accepting legal agreements, renewing the membership, and managing the team. For more information about Apple roles see [Roles and access](https://developer.apple.com/help/account/access/roles/).

!!! info
    - The Apple Developer Program annual fee is `99 USD` in local currency where available - see [Program enrollment](https://developer.apple.com/help/account/membership/program-enrollment/).
    - Eligible nonprofit organizations, accredited educational institutions, and government entities can request a [fee waiver](https://developer.apple.com/help/account/membership/fee-waivers/).

### Before You Start

Prepare the following:

- Apple Account with two-factor authentication enabled
    - If needed, see [How to create a new Apple Account](https://support.apple.com/kb/ht2534) and [Two-factor authentication for Apple Account](https://support.apple.com/102660)
- Legal entity name exactly as registered
- Public company website on the organization's own domain
- Work email address on the same domain
- [D-U-N-S Number](https://developer.apple.com/help/account/membership/D-U-N-S/) for the legal entity, or the information needed to request one
- Person enrolling must have authority to bind the organization to legal agreements, or have a reference who can confirm that authority
- iPhone, iPad, or Mac ready for the [Apple Developer app](https://developer.apple.com/help/account/membership/enrolling-in-the-app/) if Apple requires in-app identity verification

### [Enrollment](https://developer.apple.com/help/account/membership/program-enrollment/)

??? note "Steps"

    - Start the enrollment process.
    - Sign in with the client's Apple Account.
    - Choose the `Apple Developer Program` organization/company path.
    - Enter the organization's legal entity name exactly as registered.

        !!! info "Legal entity"

            Apple does not accept DBAs, fictitious businesses, trade names, or branches for organization enrollment. See [D-U-N-S Number](https://developer.apple.com/help/account/membership/D-U-N-S/).

    - Provide the organization's `D-U-N-S Number`.

        - If the organization already has one, use the existing number.
        - If not, request it through Apple's [D-U-N-S lookup flow](https://developer.apple.com/help/account/membership/D-U-N-S/).
        - Government organizations can enroll without a D-U-N-S Number.
        - D&B may take up to `5 business days` to issue the number, and Apple may need up to `2 additional business days` to receive updated information.

    - Complete Apple's verification steps.

        - Apple verifies the organization's identity and legal entity status.
        - If the person enrolling is not the owner/founder, Apple may ask for a contact who can confirm legal authority.
        - Apple may request additional business documents during verification. In some regions, these documents may need to be notarized - see [Identity verification](https://developer.apple.com/help/account/membership/identity-verification/).
        - Apple may also require in-app identity verification or photo ID depending on the region and case.

    - Wait for Apple to verify the enrollment and send the next-step email.
    - Purchase the membership when Apple confirms the enrollment is ready to complete.

        !!! info "Payment"

            Organization enrollments are paid after Apple verifies the enrollment data. See [Program enrollment](https://developer.apple.com/help/account/membership/program-enrollment/).

### Common Issues

- `D-U-N-S mismatch`: the legal name or address in D&B does not match the company's official records used during enrollment
- `Not a legal entity`: sole proprietorships, branches, and trade names are not accepted for organization enrollment
- `Domain mismatch`: Apple expects the website and work email to be associated with the organization
- `Manual review`: Apple may request additional documents or ask the client to contact Apple Developer Support
- `Later company changes`: changes to legal name, address, or membership details are handled through Apple after enrollment - see [Updating your account information](https://developer.apple.com/help/account/membership/updating-your-account-information/)

## After Enrollment

Once the membership is active:

1. Continue with [Customer Account](ios_customer_account.md).
2. Invite Futured's `ops@futured.app` user to App Store Connect.
3. Create the App Store Connect API key needed for CI/CD.

!!! info
    The `Account Holder` remains responsible for membership renewal and legal agreements. See [Program renewal](https://developer.apple.com/help/account/membership/renewal/) for Apple's renewal details.

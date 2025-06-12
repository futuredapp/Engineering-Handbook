# Google Play Account Setup

This page describes how to create and set up a Google Play account.

## Existing Google Play Account
If the client already has a Google Play account:

1. Invite the Futured account (`ops@futured.app`) to the client's Google Play Console
2. Configure the appropriate permissions (see screenshot below)

    !!! warning "TODO: Add screenshot of permission settings. Note: These vary by project."

3. Proceed to [Setting up your app in Google Play Console](./10_google_play_app.md)

## New Google Play Account

!!! danger "This step has to be done by the client - the new account owner."

The account creation process varies depending on whether you're creating a personal or organization account.

Start the registration process here: [Google Play Console Signup](https://play.google.com/console/signup). 

For detailed instructions, consult the [Play Console Getting Started Guide](https://support.google.com/googleplay/android-developer/answer/6112435).

!!! info
    - A one-time registration fee of US$25 applies to both personal and organization accounts
    - Payment must be made via credit/debit card
    - You may need to provide ID documentation or other verification materials

### Organization Account [(Documentation)](https://support.google.com/googleplay/android-developer/answer/13628312)

Required information:

- D‑U‑N‑S Number (obtain from [D&B](https://www.dnb.com/en-us/smb/duns/get-a-duns.html))
    - Select "I'm a Google developer" in the wizard (also available in Czech)
    - Standard processing time: up to 30 business days
    - Expedited processing ($200-$300): approximately 8 business days
    - [Community experiences with D-U-N-S processing times](https://www.reddit.com/r/smallbusiness/comments/1124z4t/dun_and_bradstreet_number_wait_time/)
- Developer name (can differ from legal name and can be changed later)
- Organization details (name, address, phone, website)
- Contact information (email and phone) for Google communications
- Developer contact information (email and phone) for your Google Play profile

!!! info
    The following information will be publicly visible on Google Play:
    
    - Organization name and address
    - Developer name and email
    - Developer phone number


### Personal Account [(Documentation)](https://support.google.com/googleplay/android-developer/answer/13628312)

Required information:

- Developer name (can differ from legal name and can be changed later)
- Legal name and address
- Contact information (email and phone) for Google communications
- Developer email (will be visible in your Google Play profile)

!!! warning
    The main difference between a personal and organization account is that the personal account is not associated with a D-U-N-S number, **however your legal (personal) name and address will be publicly visible on Google Play**.
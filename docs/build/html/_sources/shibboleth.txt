Shibboleth configuration
------------------------

First follow the steps outlined here -
http://edx.readthedocs.io/projects/edx-installing-configuring-and-running/en/latest/configuration/tpa/

After 4.16.3 section, go to /edx/app/edxapp/lms.env.json and add the following lines to the end just before the closing ‘]’ -

.. code-block:: shell
    
    "THIRD_PARTY_AUTH_BACKENDS": [
        "third_party_auth.saml.SAMLAuthBackend"
    ]

Next, in Django admin for LMS, under third_party_auth section, in Provider Configuration (SAML IdP), add SAML Provider configuration and fill it with the data available in IU KB here - https://kb.iu.edu/d/bdgs

The metadata column in the above page should me marked done after a couple of minutes.

Only for integrating with Indiana University:

1. follow the steps outlined in the IU KB - https://kb.iu.edu/d/bdag
2. After this setup, you should see the login with IU button.

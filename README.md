# oidc4vc-static-resolver

[Japanese version (日本語版)](README_ja.md)

- **5-Minute VC Issuance**: Build a VC issuing server just by pushing to GitHub Pages.
- **Latest Standards**: Supports OIDC4VCI (Draft 13+) / SD-JWT VC / did:web.

## Demo
You can see our live demo site here:
[https://tonari-no-usagi.github.io/oidc4vc-static-resol/](https://tonari-no-usagi.github.io/oidc4vc-static-resol/)

## Tested Wallets
Successfully verified with the following wallet apps:
- **Lissi ID-Wallet** (iOS/Android)

## Core Features

1. Metadata Generator
    - Generates .well-known directory structures compliant with RFC 9101 and OpenID4VCI standards.
    - Uses strong Go types to ensure accurate JSON schema generation.

2. SD-JWT VC Generation
    - Pre-generates "Pre-authorized" VCs at build time.
    - Supports SD-JWT (Selective Disclosure JWT) format with masked claims and disclosure hashes.

3. did:web Support
    - Automatically generates compliant did.json for the issuer.
    - Correctly handles domain and path mapping for did:web identifiers.

4. Interactive Demo HTML
    - Generates an index.html with a QR code containing openid-initiate-issuance:// custom schemes.
    - Allows direct VC acquisition from mobile wallet apps.

## System Architecture

The tool generates the following structure for GitHub Pages:

```text
/public
|-- .well-known
|   |-- openid-credential-issuer  # Issuer metadata
|   |-- did.json                  # did:web document
|   `-- oauth-authorization-server # Authorization server metadata
|-- credentials
|   `-- push-metadata             # Pre-signed SD-JWT VC
`-- index.html                    # GUI with QR code for wallet scanning
```

## Quick Start

### Local Build
1. Configure your issuer in `issuer.yaml`.
2. Run the build command:
   ```bash
   go run main.go build
   ```
3. Deploy the contents of the `public` directory to GitHub Pages.

### GitHub Actions (Automation)
Add the following workflow to your repository (`.github/workflows/deploy.yml`) to automate the generation and deployment.

```yaml
name: Deploy VC Issuer
on:
  push:
    branches: [ main ]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
      - name: Build Metadata
        uses: tonari-no-usagi/oidc4vc-static-resol-actio@main
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

## Technical Context

This project is built for the 2026 digital identity ecosystem, focusing on EUDI Wallet / eIDAS 2.0 interoperability and SD-JWT format standards.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

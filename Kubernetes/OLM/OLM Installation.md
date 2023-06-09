
**OLM** stands Operator Life cycle Manager. 
OLM provides many templates for installing different applications. It can make complex installations easier.

## Install from GitHub release[](https://sdk.operatorframework.io/docs/installation/#install-from-github-release)

This process will install an installer on your local machine. Once installed, and if you have kubectl installed, you can run the binary and it will setup the OLM on your kubernetes cluster.

#### Prerequisites[](https://sdk.operatorframework.io/docs/installation/#prerequisites)

-   [curl](https://curl.haxx.se/)
-   [gpg](https://gnupg.org/) version 2.0+

#### 1. Download the release binary[](https://sdk.operatorframework.io/docs/installation/#1-download-the-release-binary)

Set platform information:

```sh
export ARCH=$(case $(uname -m) in x86_64) echo -n amd64 ;; aarch64) echo -n arm64 ;; *) echo -n $(uname -m) ;; esac)
export OS=$(uname | awk '{print tolower($0)}')
```

Download the binary for your platform:

```sh
export OPERATOR_SDK_DL_URL=https://github.com/operator-framework/operator-sdk/releases/download/v1.27.0
curl -LO ${OPERATOR_SDK_DL_URL}/operator-sdk_${OS}_${ARCH}
```

#### 2. Verify the downloaded binary[](https://sdk.operatorframework.io/docs/installation/#2-verify-the-downloaded-binary)

Import the operator-sdk release GPG key from `keyserver.ubuntu.com`:

```sh
gpg --keyserver keyserver.ubuntu.com --recv-keys 052996E2A20B5C7E
```

Download the checksums file and its signature, then verify the signature:

```sh
curl -LO ${OPERATOR_SDK_DL_URL}/checksums.txt
curl -LO ${OPERATOR_SDK_DL_URL}/checksums.txt.asc
gpg -u "Operator SDK (release) <cncf-operator-sdk@cncf.io>" --verify checksums.txt.asc
```

You should see something similar to the following:

```console
gpg: assuming signed data in 'checksums.txt'
gpg: Signature made Fri 30 Oct 2020 12:15:15 PM PDT
gpg:                using RSA key ADE83605E945FA5A1BD8639C59E5B47624962185
gpg: Good signature from "Operator SDK (release) <cncf-operator-sdk@cncf.io>" [ultimate]
```

Make sure the checksums match:

```sh
grep operator-sdk_${OS}_${ARCH} checksums.txt | sha256sum -c -
```

You should see something similar to the following:

```console
operator-sdk_linux_amd64: OK
```

#### 3. Install the release binary in your PATH[](https://sdk.operatorframework.io/docs/installation/#3-install-the-release-binary-in-your-path)

```sh
chmod +x operator-sdk_${OS}_${ARCH} && sudo mv operator-sdk_${OS}_${ARCH} /usr/local/bin/operator-sdk
```

#### 4. Install the OLM on your kubernetes clustter 
First ensure that you have kubectl installed and that your context is set to the cluster you want to install the OLM on.

```sh
operator-sdk olm install
```


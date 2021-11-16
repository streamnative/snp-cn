---
title: Update license
id: license-update
category: operator-guides
---

You can try StreamNative Platform with a 30-day trial license. After 30 days, StreamNative Platform requires you to purchase a license. Contact StreamNative Platform sales to apply for your license file from StreamNative. Once you receive the file from StreamNative, you can upload your license.

To update the license for StreamNative Platform, follow these steps.

1. Enter the `toolset` Pod.

    To use the official Pulsar CLI tools, such as pulsar-client, StreamNative Platform provides a `toolset` Pod. you can use the `kubectl exec` command to connect to the Pod.

    ```
    kubectl exec -n KUBERNETES_NAMESPACE -it RELEASE_NAME-sn-platform-toolset-0 -- bash
    ```

2. Add the environment variable for the authentication token.

    ```
    export AUTH_TOKEN=`cat /pulsar/tokens/client/token`
    ```

3. Update the license information based on the license file that you received from StreamNative.

    ```
    curl -XPOST -H "Authorization:Bearer $AUTH_TOKEN" http://RELEASE_NAME-sn-platform-broker-headless:8080/snp/admin/v1/license -d '{
    "license": {
        "uid": "f8b79e9e-fb15-11eb-9412-bd766dc06cb8",
        "type": "enterprise",
        "issue_date_in_millis": 1628380800000,
        "expiry_date_in_millis": 1660262400000,
        "start_date_in_millis": 1628726400000,
        "max_cpu_cores": 10,
        "issued_to": "StreamNative Ltd.",
        "issuer": "binw@streamnative.io",
        "signature": "AAAAAQAAAA3QvaexD1qhAxUhWkOoAAAAIKEWNm+fXOi/V7uWBym/nC1KED2o527crKp1yZ5ahNGbAAABAEDcgIjhMfojDcs68Q26FLQ8ilsdiJvqDL2LxC1JUSS1e2NgOuIPhPruKdzJAVYmfXtAGjwVRtcUX45k+TtyI2l+5qlmt+BuS/5RVPQL524XC6Ptygp0N"
    }
    }'
    ```

    **Output**
    ```
    active
    ```

    Once you see `active` in the output, it indicates that the license is updated successfully.
---
title: 更新许可证
id: license-update
category: operator-guides
---

StreamNative Platform 提供 30 天的试用许可证让用户体验。30 天后，StreamNative Platform 将要求用户购买许可证。请联系 StreamNative Platform 的销售人员，向 StreamNative 申请你的许可证文件。从 StreamNative 收到文件后，就可以上传许可证。

按照如下步骤更新 StreamNative Platform 许可证：

1. 进入 `toolset` Pod。

    可以通过 StreamNative Platform 提供的 `toolset` Pod，使用官方 Pulsar CLI （命令行工具），例如 pulsar-client。你可以用 `kubectl exec` 命令来连接到这个 Pod。

    ```
    kubectl exec -n KUBERNETES_NAMESPACE -it RELEASE_NAME-sn-platform-toolset-0 -- bash
    ```

2. 为认证 token 添加环境变量。

    ```
    export AUTH_TOKEN=`cat /pulsar/tokens/client/token`
    ```

3. 根据从 StreamNative 收到的许可证文件，更新许可证信息。

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

    **输出**
    
    ```
    active
    ```
    
    输出为 `active` 表示许可证已成功更新。
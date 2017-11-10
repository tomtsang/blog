
===================
ceph auth 
===================


::

    cephu@cephadmin:~/my-cluster$ ceph auth ls
    installed auth entries:

    mds.node1
            key: AQADpu5ZJgEAAhAA6Om+UrlNn3s4v728UGIuTQ==
            caps: [mds] allow
            caps: [mon] allow profile mds
            caps: [osd] allow rwx
    osd.0
            key: AQCAou5ZbAFBCRAANHUYj3trYJm+bbpzM68Czw==
            caps: [mgr] allow profile osd
            caps: [mon] allow profile osd
            caps: [osd] allow *
    osd.1
            key: AQCNou5Z0AEiChAAoXaQS1KsqnygVzIdK4LeBQ==
            caps: [mgr] allow profile osd
            caps: [mon] allow profile osd
            caps: [osd] allow *
    osd.2
            key: AQCaou5ZckceFxAArxSTMrgL94Pe9wGqhT2tGA==
            caps: [mgr] allow profile osd
            caps: [mon] allow profile osd
            caps: [osd] allow *
    client.admin
            key: AQCtj+5ZnNTvGRAA2RxAGcIQZJnaJSPEz4jdGw==
            caps: [mds] allow *
            caps: [mgr] allow *
            caps: [mon] allow *
            caps: [osd] allow *
    client.bootstrap-mds
            key: AQCuj+5ZL4meKBAAQ/AJ/63mw4XD3HIcx1+ojw==
            caps: [mon] allow profile bootstrap-mds
    client.bootstrap-mgr
            key: AQDYne5Zf3L3BxAAt/PZfcXS1b0ioDrgZX8v8w==
            caps: [mon] allow profile bootstrap-mgr
    client.bootstrap-osd
            key: AQCtj+5ZEe+yMhAAX8vyaP2g7cBznN5dk48mBQ==
            caps: [mon] allow profile bootstrap-osd
    client.bootstrap-rbd
            key: AQCvj+5Zpj97BRAAeBH22SI5YQ8+iI4+V+Kopg==
            caps: [mon] allow profile bootstrap-rbd
    client.bootstrap-rgw
            key: AQCuj+5ZRqQKEBAABXu387mLGQGPwkvcVBhPyg==
            caps: [mon] allow profile bootstrap-rgw
    client.kubernetes-dynamic-user-02edf53a-bec3-11e7-bda7-000c299a346f
            key: AQBTV/lZenwMKhAAFI7qG14jcFj6kScyg65DsQ==
            caps: [mds] allow r
            caps: [mon] allow r
            caps: [osd]
    client.kubernetes-dynamic-user-34d65a6c-bed2-11e7-bda7-000c299a346f
            key: AQDRcPlZ9MBSNBAAsDbLR8rEWslQmq9fhcsZrw==
            caps: [mds] allow r,allow rw path=/volumes/kubernetes/kubernetes-dynamic-pvc-34d659ff-bed2-11e7-bda7-000c299a346f
            caps: [mon] allow r
            caps: [osd] allow rw pool=cephfs_data namespace=fsvolumens_kubernetes-dynamic-pvc-34d659ff-bed2-11e7-bda7-000c299a346f
    client.kubernetes-dynamic-user-34fb64a9-be15-11e7-8636-000c299a346f
            key: AQC7M/hZxmS/DhAAZR/ybQ7WSBEvmF3ZYMarRw==
            caps: [mds] allow r
            caps: [mon] allow r
            caps: [osd]
    client.kubernetes-dynamic-user-612fd2ba-be13-11e7-8636-000c299a346f
            key: AQCqMPhZ0fWTFxAAN5nMfYyNHjMqVamVjmQsCA==
            caps: [mds] allow r
            caps: [mon] allow r
            caps: [osd]
    client.rgw.ceph-client
            key: AQDoM/BZiyLcHBAASXop8G7HJPJQfVYDrA5BkQ==
            caps: [mon] allow rw
            caps: [osd] allow rwx
    client.rgw.node1
            key: AQCGr+5ZQDQRLRAAw9WB8JbSUSeUkJ7k7+cCAg==
            caps: [mon] allow rw
            caps: [osd] allow rwx
    mgr.mon1
            key: AQDvne5ZbU0JOhAAotdp7L9xiVaL54awr26lcA==
            caps: [mds] allow *
            caps: [mon] allow profile mgr
            caps: [osd] allow *
    mgr.node2
            key: AQBhqO5ZEy2gMxAAfG496CAtCeVdApXfffwf+Q==
            caps: [mds] allow *
            caps: [mon] allow profile mgr
            caps: [osd] allow *
    mgr.node3
            key: AQBjqO5ZM0l9HBAALhWmqYNbpOFUvPfHv4aOug==
            caps: [mds] allow *
            caps: [mon] allow profile mgr
            caps: [osd] allow *
    cephu@cephadmin:~/my-cluster$ ceph auth get client.kubernetes-dynamic-user-02edf53a-bec3-11e7-bda7-000c299a346f
    exported keyring for client.kubernetes-dynamic-user-02edf53a-bec3-11e7-bda7-000c299a346f
    [client.kubernetes-dynamic-user-02edf53a-bec3-11e7-bda7-000c299a346f]
            key = AQBTV/lZenwMKhAAFI7qG14jcFj6kScyg65DsQ==
            caps mds = "allow r"
            caps mon = "allow r"
            caps osd = ""
    cephu@cephadmin:~/my-cluster$ ceph auth ls | grep kubernetes-dynamic-pvc-34d659ff-bed2-11e7-bda7-000c299a346f
    installed auth entries:

            caps: [mds] allow r,allow rw path=/volumes/kubernetes/kubernetes-dynamic-pvc-34d659ff-bed2-11e7-bda7-000c299a346f
            caps: [osd] allow rw pool=cephfs_data namespace=fsvolumens_kubernetes-dynamic-pvc-34d659ff-bed2-11e7-bda7-000c299a346f
    cephu@cephadmin:~/my-cluster$ 
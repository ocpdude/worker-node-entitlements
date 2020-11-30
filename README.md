## Add Your RHEL Entitlments To Your Worker Nodes

Ref - YouTube video : https://youtu.be/rezoliup6i0

Since moving more toward RHCOS for the underlying OCP nodes, and away from RHEL. It may be required to add your entitlements to your worker nodes to support builds and buildconfigs. Using MachineConfigs, here is an easy way to do it.

1. Grab the following files from a valid RHEL subscriptions, for me, I will choose a host that is registred, but one that isn't used for much else. This way any subscription notifications I receive, would then apply to my OCP cluster.
    ```
    /etc/rhsm/rhsm.conf
    /etc/pki/entitlement/<NUMBER-key.pem>
    /etc/pki/entitlement/<NUMBER.pem>
    ```
2. Encode these files and then transfer those values into their respective areas in the `99-worker-entitlements.yaml` file. \
    `cat {rhsm.conf, <NUMBER-key.pem>, <NUMBER.pem>} | base64 -w0 > $value.64`

3. Install the MachineConfig \
`oc create -f 99-worker-entitlements.yaml`

4. Monitor the nodes and re-test when the nodes are back online. \
    ##### Note: The previous step will cause each worker node to install the entitlements and reboot. The HA nature of OCP should prevent any downtime; however, it is always recommended to perform MachineConfig updates during a maintenace window. 
    `watch oc get nodes`

5. Test file... \
`oc create ubi8-build-test.yaml` \
`oc logs ${podId}`

# vaultingkube

![logo](resource/vaultingkube.png)

Take config maps and secrets stored inside Hashicorp Vault and sync them to your Kubernetes cluster.

## How it works

After Vaultingkube is running in your cluster it will look at the Vault server configured via the Vault client config options.  Based on the `VK_VAULT_ROOT_MOUNT_PATH` Vaultingkube will read all kv secrets it has access to and reference any with a matching mount path.  EG if `VK_VAULT_ROOT_MOUNT_PATH` is set to `vaultingkube/my-cluster` it will look at all mounts at `vaultingkube/my-cluster/*`.

The type of secret and namespace are configured in the mount path as well.  It looks like `[VK_VAULT_ROOT_MOUNT_PATH]/[NAMESPACE]/[SECRET_TYPE]/[NAME]`.  If I wanted to create a configmap in the `default` namespace named `tom` I would create a kv secret at `[VK_VAULT_ROOT_MOUNT_PATH]/default/configmaps/tom`.

Vaultingkube will only overwrite, manage, or delete ConfigMaps and Secrets that have the annotation `vaultingkube.io/managed: "true"` set.  You will need to manually set this on existing Secrets and Configmaps for Vaultingkube to take over.

Vaultingkube does not have any logic to determine if Vault has changed, and so it uses the `VK_SYNC_PERIOD` environment variable to determine how frequently (in seconds) it sends update requests to Kubernetes to stay in sync with Vault.

By default Vaultingkube will delete ConfigMaps and Secrets that exist in Kubernetes and **not** Vault that have the annotation of `vaultingkube.io/managed: "true"`.  To turn this off set the environment variable `VK_DELETE_OLD` to `"false"`.

## Deployment

See [deployment/README.md](deployment/README.md).

## Client Versions

You should be able to infer the supported versions of Kubernetes and Vault that will work with this program from these.

- client-go: 5.0.1
- vault: 0.9.0

## Environment Variables

| Variable                 | Explanation                                                         |
|--------------------------|---------------------------------------------------------------------|
| VK_DELETE_OLD            | Delete CM/Secret's that are in Kube but not Vault [Default: true]   |
| VK_SYNC_PERIOD           | How many seconds to wait in between syncs [Default: 300]            |
| VK_VAULT_ROOT_MOUNT_PATH | Path to configmaps and secrets [Example: vaultingkube/cluster-name] |

## License

Apache 2

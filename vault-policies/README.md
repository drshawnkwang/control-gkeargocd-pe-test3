# Vault policies

A place to store Vault-server policies that are specific to this cluster.

## Instructions

1) Login to vault

```shell
export VAULT_ADDR="https://vault.es.net:8200"
vault login -method ldap
export VAULT_NAMESPACE=k8s
```

2) Create a policy

For example, create the file `my-cluster-name.hcl`.

Note: You may use wildcards to give the policy more secrets to access, or you may give access to individual kv secrets by using their full paths.

```hcl
path "kv/data/my-cluster-name/*" {
  capabilities = ["read", "list"]
}
```

Apply the new policy

```shell
vault policy write my-cluster-name-policy vault-policies/my-cluster-name.hcl
```

3) Update the esorole policy

Make sure the list of policies contains all the existing policies; else the role will lose access to those secrets which may break applications in the cluster! Use `vault read` to view existing policies

```shell
# List existing role with policies
vault read auth/<CLUSTER-PATH>/role/esorole

# Use cluster-path and add the new policy created
vault write auth/<CLUSTER-PATH>/role/esorole policies=default,netscaler-policy,client-rancher-policy,NEWPOLICY

# example
vault write auth/seg-ocd-staging-west2/role/esorole policies=default,netscaler-policy,client-rancher-policy,seg-ocd-staging-west2
```

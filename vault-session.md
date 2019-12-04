# Vault workshop

### Hands-on session
* Slides: https://hashicorp.github.io/workshops/azure/vault/#1
* High-level architecture for vault looks like this:
![vault-high-level-architecture](vault-high-level-architecture.png)
(More details here: https://www.hashicorp.com/identity-based-security-and-low-trust-networks)
* Vault internals look like this:
![vault-internals](vault-internals.png)
(More details here: https://www.vaultproject.io/docs/internals/architecture.html)
* Vault high availability setup looks like this:
![vault-high-availability](vault-high-availability.png)
(More details here: https://learn.hashicorp.com/vault/operations/ops-reference-architecture)

### Chapter 2
* Setting up the vault environment using Terraform (this is a continuation from the Terraform session)

### Chapter 3
* Connecting to vault
```bash
terraform output Vault_Server_URL
```
This outputs something like this:
```bash
 http://kaushik.eastus.cloudapp.azure.com:8200
```
* Log into the vault
* Create a new secret (via the UI). For example:
![creating-new-secret](creating-new-secret.png)
* Get the secret (via SSH). For example:
```bash
hashicorp@kaushik:~$ vault kv get kv/department/team/mysecret
```
This outputs something like this:
```bash
====== Metadata ======
Key              Value
---              -----
created_time     2019-12-04T14:42:47.988736609Z
deletion_time    n/a
destroyed        false
version          1

====== Data ======
Key         Value
---         -----
rootpass    supersecret
```
* Get the secret (via CURL). For example:
```bash
curl --header "X-Vault-Token: root" \
http://localhost:8200/v1/kv/data/department/team/mysecret | jq .data
```
This outputs something like this:
```json
{
  "data": {
    "rootpass": "supersecret"
  },
  "metadata": {
    "created_time": "2019-12-04T14:42:47.988736609Z",
    "deletion_time": "",
    "destroyed": false,
    "version": 1
  }
}
```

### Chapter 4
* This is where we create some ACL policies
* Example of what an ACL policy looks like from the UI is as follows:
![vault-acl-policies](vault-acl-policies.png)

### Chapter 5
* Adding the username-password as an Access method. Here is how it looks on the UI:
![vault-auth-methods](vault-auth-methods.png)
* One the userpass method is added, we can create different access methods on the SSH-ed machine:
```bash
vault write auth/userpass/users/bob \
    password=foo \
    policies=secret
vault write auth/userpass/users/sally \
    password=foo \
    policies=lob_a
```
* This means that Bob can read, list and create data under the secret/* path because his policy allows him to do so. Vault comes with a key/value engine mounted at secret/ by default. When Sally logs on she can't even see the secret/ path because she does not have list permissions

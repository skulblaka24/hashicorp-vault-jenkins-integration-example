Jenkins pipeline example to access vault secrets
================================================

Instructions:
-------------

A. **On Vault:**

1. Generate token
   
      Connect to vault using the root token.

      Create a policy:
      	* Policies tab > ACL Policies > Create ACL policy
      	* Add a name: jenkins
      	* Add a Policy:

   		path "kv/*" {
   		  capabilities = ["read","list"]
   		}

      Generate a token:

      ```shell
      $ vault token create -policy=jenkins
      ```

2. Create Approle configuration (From API or CLI)
   
      Enable the approle backend:

      ```shell
      $ vault auth-enable approle
      ```     

      Create the approle for Jenkins:

      ```shell
      $ vault write auth/approle/role/jenkins \
      	secret_id_ttl=60m \
      	token_ttl=60m \
      	token_max_tll=120m \
      	policies="jenkins"
      ```

      Create the approle for Jenkins:

      ```shell
      $ vault write auth/approle/role/jenkins \
      	secret_id_ttl=60m \
      	token_ttl=60m \
      	token_max_tll=120m \
      	policies="jenkins"
      ```

      Get the role-id and secret-id for storing in Jenkins:

      ```shell
      $ vault read auth/approle/role/jenkins/role-id</code></pre>
      $ vault write -f auth/approle/role/jenkins/secret-id</code></pre>
      ```

3. Create a secret:

      Secrets tab > Enable new engine > KV

      Leave Path KV and Version 2
      or

      ```shell
      $ vault secrets enable -version=2 -path=kv kv
      ```

      Click on "Create secret"

      Path for this secret: teb

      Key: token

      Value: s.XXXXXXXXXXXXXXX <Token generated above>
      or

      ```shell
      $ vault kv put kv/teb token=<Token generated above>
      ```

B. **On jenkins:**

1. Vault Token Credential Creation:

   * Open Jenkins credential creation page:
   * Manage Jenkins > Manage Credentials
   * Click on the jenkins store
   * Then the global credentials domain
   * On the left side click on: "Add Credentials"
   * then use the following parameters:
   * Kind: Vault Token Credential
   * Scope: Global
   * Token: Vault Token
   * ID: vault-token-access
   * Description: A description

2. Vault Approle Credential Creation:

   * Open Jenkins credential creation page:
   * Manage Jenkins > Manage Credentials
   * Click on the jenkins store
   * Then the global credentials domain
   * On the left side click on: "Add Credentials"
   * then use the following parameters:
   * Kind: Vault App Role Credential
   * Scope: Global
   * Role ID: Vault Role ID
   * Secret ID: Vault Secret ID
   * Path: approle path (approle)
   * ID: vault-approle-access
   * Description: A description

3. Create a pipeline

   * Dashboard > New Item
   * Choose a name: vault-pipeline
   * Select: Pipeline
   * Click "OK"

   * Go to the end and in "definition", select "Pipeline Script from SCM".
   * As SCM, choose GIT.
   * Add your repository url where you stored your jenkinsfile.
   * Then click "Save" and run it!

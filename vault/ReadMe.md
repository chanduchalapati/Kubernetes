echo $VAULT_UNSEAL_KEY
5JXLQxtKE3IKGdlVX56q2SR+iLU8qr9iKhvyINTa1co=

"root_token": "s.2na4zEFqzhmNi5C7sMlUNAtN"

Key                  Value
---                  -----
token                s.2na4zEFqzhmNi5C7sMlUNAtN
token_accessor       BjjJihGsIs9PWsl7KVExyN4a
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]

vault kv put my-app/sekhar name=devopssekhar
vault kv get my-app/sekhar

vault policy write demo-policy - <<EOH
path "my-app/*" {
  capabilities = ["read"]
}
EOH

vault write auth/kubernetes/config \
    token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
    kubernetes_host=https://127.0.0.1:443 \
    kubernetes_ca_cert=@ca.crt

    curl --request POST \
    --data '{"jwt": "'$jwt_token'", "role": "webapp"}' \
    http://10.106.59.147:32000/v1/auth/kubernetes/login

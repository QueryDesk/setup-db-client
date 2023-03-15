# Setup Database Client (QueryDesk)

Automate setting up a database client using https://querydesk.com so your 
team has instant access for debugging and testing.

This action can be called multiple times with the same id, which will update the connection.

## Usage

```yaml
jobs:
  create_db:

    steps:
    # ...

    - name: Setup Database Client
      uses: QueryDesk/setup-db-client@v1
      with:
        api-key: ${{ secrets.QUERYDESK_API_KEY}}
        id: pr-${{ github.event.pull_request.number }} # your workflow trigger must be `pull_request` for this to work
        hostname: localhost
        database: database_name
        adapter: postgres
        username: my_user
        password: my_password

    # ...
```

## Inputs

-   `api-key`: (Required) You will need to create an API key on 
    https://app.querydesk.com/api-keys and save it as a secret in GitHub 
    Actions settings.
    
    Example API key:

    ```text
    SFMyNTY.g2gDbQAAAB5rZXlfMDFHSDRHWkFFVkVIOThCWlhXRzZFMjNOUlduBgDh2w9JhAFiAAFRgA.SeeINPdFn2cz6kqnkPb7IE7B9OLnc840R--hWiRoTYg
    ```

-   `id`: (Required) A unique id used to interact with this resource through the API.

-   `name`: (Required)  The name for users to use to identity the database.

-   `hostname`: (Required) The hostname for connecting to the database, either an ip or url.

-   `database`: (Required) The name of the database to connect on.

-   `adapter`: (Required) The adapter to use to establish the connection. 
    Currently only `postgres` is supported, but mysql and sql server are
    on the roadmap.
    
-   `username`: (Required) The username to authenticate with.

-   `password`: (Required) The password to authenticate with. This is encrypted
    with field level encryption and is never exposed to users of the database
    client.

-   `agent-id`: (Optional) If your database is in a private network inaccessible
    to the public internet you will need to setup an agent. You can find instructions
    for setting up an agent here: https://github.com/QueryDesk/agent. 
    
    Example agent id:

    ```text
    agt_01GH0HABH34YWYV1E66YXXM6HG
    ```

-   `ssl`: (Optional) Set to `enabled` to turn on ssl connections for this database.

-   `ssl_ca_cert`: (Optional) The server ca cert to use with ssl connections, `ssl` 
    __must__ be set to `enabled`. This is encrypted with field level encryption and is 
    never exposed to users of the database client. __Must__ be base64 encoded.

-   `ssl_key`: (Optional) The client key to use with ssl connections, `ssl` 
    __must__ be set to `enabled`. This is encrypted with field level encryption and is 
    never exposed to users of the database client. __Must__ be base64 encoded.

-   `ssl_cert`: (Optional) The client cert to use with ssl connections, `ssl` 
    __must__ be set to `enabled`. This is encrypted with field level encryption and is 
    never exposed to users of the database client. __Must__ be base64 encoded.

-   `host`: (Optional) The host to make the api call to, defaults to api.querydesk.com.

## Examples

### Setting up with SSL

This example demonstrates setting up a database with ssl connections. Make sure ssl certs/key are base64 encoded.

```yaml
jobs:
  create_db:
    # ...

    steps:
    # ...

    - id: create-db
      name: Create Database
      uses: some-action
    
    - name: Setup Database Client
      uses: QueryDesk/setup-db-client@v1
      with:
        api-key: ${{ secrets.QUERYDESK_API_KEY}}
        id: ...
        hostname: ...
        database: ...    
        adapter: ...
        username: ...    
        password: ...  
        ssl: enabled  
        ssl_ca_cert: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVCakNDQXU2Z0F3SUJBZ0lKQU1jMFp6...
        ssl_key: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVCakNDQXU2Z0F3SUJBZ0lKQU1jMFp6...
        ssl_cert: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVCakNDQXU2Z0F3SUJBZ0lKQU1jMFp6...
```

### Sourcing credentials from previous step

This example demonstrates setting up a database that is setup from a previous step. The output names may differ depending on how your database is created.

```yaml
jobs:
  create_db:
    # ...

    steps:
    # ...

    - id: create-db
      name: Create Database
      uses: some-action
    
    - name: Setup Database Client
      uses: QueryDesk/setup-db-client@v1
      with:
        api-key: ${{ secrets.QUERYDESK_API_KEY}}
        id: pr-${{ github.event.pull_request.number }} # your workflow trigger must be `pull_request` for this to work
        hostname: ${{ steps.create-db.outputs.hostname }}    
        database: ${{ steps.create-db.outputs.database }}    
        adapter: postgres
        username: ${{ steps.create-db.outputs.username }}    
        password: ${{ steps.create-db.outputs.password }}    
```

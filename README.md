# Local chat agent

_Tested with Windows 10._

**Requrements**

- Docker [https://www.docker.com](https://www.docker.com)
- Jan [https://jan.ai](https://jan.ai)

## init project

```
mkdir local-chat-agent
cd local-chat-agent
```

## get project and start docker

```
git clone https://github.com/anguszh/jan-ai.git
cd jan-ai
docker compose --profile cpu up -d
```

## start Api Server

- start jan.ai settings
- enter for API Key **"mylocalapi"**
- press button "Start Server"

```
http://127.0.0.1:1337
```

## Examples

### example show window command

```
curl -X POST http://localhost:1337/v1/chat/completions ^
 -H "Content-Type: application/json" ^
 -H "Authorization: Bearer mylocalapi" ^
 -d "{\"model\": \"Qwen2.5-VL-3B-Instruct-iq4_xs\", \"messages\": [{\"role\": \"user\", \"content\": \"Respond only with the Windows command to display the current user.\"}]}"
```

### example say hello

```
curl -X POST http://localhost:1337/v1/chat/completions ^
 -H "Content-Type: application/json" ^
 -H "Authorization: Bearer mylocalapi" ^
 -d "{\"model\": \"Qwen2.5-VL-3B-Instruct-iq4_xs\", \"messages\": [{\"role\": \"user\", \"content\": \"Hello Jan\"}]}"
```

## example as powershell script

_create powershell script file **"askjan.ps1"**:_

```
param(
    [Parameter(Mandatory = $true, Position = 0)]
    [string]$Prompt
)

# API
$Model   = 'Qwen2.5-VL-3B-Instruct-iq4_xs'
$ApiKey  = 'mylocalapi'
$Url     = 'http://localhost:1337/v1/chat/completions'

# JSON-Body
$Payload = @{
    model    = $Model
    messages = @(
        @{
            role    = 'user'
            content = $Prompt
        }
    )
} | ConvertTo-Json -Compress

# Request
try {
    $Response = Invoke-RestMethod -Uri $Url -Method Post -Headers @{
        'Authorization' = "Bearer $ApiKey"
        'Content-Type'  = 'application/json'
    } -Body $Payload

    # only show model answer
    $Response.choices[0].message.content
}
catch {
    Write-Error "Error on API-call: $_"
}

```

### start the powershell script

```
powershell -NoProfile -ExecutionPolicy Bypass -File "C:\Users\%USERNAME%\Desktop\askjan.ps1" "Antworte nur mit dem Windows-Befehl, um den aktuellen Benutzer anzuzeigen."
```

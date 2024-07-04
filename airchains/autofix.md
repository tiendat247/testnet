
Create a Tmux session

```tmux new -t fix```

Create file fix.sh

```nano fix.sh```

Copy and paste below code to fix.sh and save by Ctrl+O, Enter, Ctrl+x

After that, run script with command

```bash fix.sh```

Close Tmux by using Ctrl+B,D

######Code#######
```
#!/bin/bash

# Define service name and log search strings
service_name="trackd"
error_pattern1="ERR Error in SubmitPod Transaction Error="
error_pattern2=(
    "with gas used"
    "Failed to Transact Verify pod"
    "Failed to Init VRF"
    "VRF record is nil"
    "Failed to Validate VRF"
    "Switchyard client connection error"
)
error_pattern3="Failed to get transaction by hash: not found"
max_retries=3
restart_delay=180  # Restart delay in seconds (3 minutes)

echo "Script started and it will handle errors for $service_name..."

while true; do
  # Get the last 10 lines of service logs
  logs=$(systemctl status "$service_name" --no-pager | tail -n 10)

  # Check for error pattern 1 in the logs
  if [[ "$logs" =~ $error_pattern1 ]]; then
    echo "Found error pattern 1 in logs, stopping $service_name..."
    systemctl stop "$service_name"
    cd ~/tracks

    echo "Service $service_name stopped, starting rollback..."
    go run cmd/main.go rollback
    go run cmd/main.go rollback
    go run cmd/main.go rollback
    echo "Rollback completed, starting $service_name..."
    systemctl start "$service_name"
    echo "Service $service_name started"
  fi

  # Check for error pattern 2 in the logs
  error_found=false
  for pattern in "${error_pattern2[@]}"; do
    if [[ "$logs" =~ $pattern ]]; then
      error_found=true
      break
    fi
  done

  # If error pattern 2 is found, restart the service
  if $error_found; then
    echo "Found error pattern 2 in logs, restarting $service_name..."
    systemctl restart "$service_name"
    echo "Service $service_name restarted"
  fi

  # Check for error pattern 3 in the logs and count consecutive occurrences
  consecutive_count=0
  if [[ "$logs" =~ $error_pattern3 ]]; then
    (( consecutive_count++ ))
    echo "Found error pattern 3 in logs, consecutive count: $consecutive_count"
  fi

  # If consecutive_count reaches max_retries, restart the service
  if (( consecutive_count >= max_retries )); then
    echo "Reached maximum retries ($max_retries) for error pattern 3, restarting $service_name..."
    systemctl restart "$service_name"
    echo "Service $service_name restarted"
    consecutive_count=0  # Reset consecutive count after restart
  fi

  # Sleep for the restart delay
  sleep "$restart_delay"
done

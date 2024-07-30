
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
error_patterns=(
	"account sequence mismatch"
	"Switchyard client connection error"
	"VRF record is nil"
	"Error in InitVRF transaction"
	"Error in SubmitPod Transaction"
	"Error in VerifyPod transaction"
	"Error Request rate limited"
	"Error in ValidateVRF transaction"
	"Failed to Validate VRF"
	"Failed to Init VRF"
	"Failed to Transact Verify pod"
	"Failed to get transaction"
)

restart_delay=300  # Restart delay in seconds (5 minutes)

while true; do
  echo "Checking logs for service $service_name at $(date)..."

  # Get the last 10 lines of service logs
  logs=$(journalctl -u "$service_name" -n 10 --no-pager)

  # Check for error patterns in the logs
  for pattern in "${error_patterns[@]}"; do
    if [[ "$logs" == *"$pattern"* ]]; then
      echo "Found error pattern in logs: $pattern"
      echo "Stopping $service_name..."
      systemctl stop "$service_name"
      cd ~/tracks

      echo "Service $service_name stopped, starting rollback..."
      for i in {1..3}; do
        go run cmd/main.go rollback
      done

      echo "Rollback completed, starting $service_name..."
      systemctl start "$service_name"
      echo "Service $service_name started"
    fi
  done

  # Sleep for the restart delay
  sleep "$restart_delay"
done
```

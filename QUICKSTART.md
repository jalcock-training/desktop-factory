# 1. Prepare the host
./bin/host-setup # logout / login

# 2. Fetch + verify Rocky base image
./bin/fetch-base

# 3. Generate cloud-init seed for browser profile
./bin/make-seed browser

# 4. Launch the disposable desktop
./bin/launch browser

# 5. Connect via RDP
./bin/rdp browser


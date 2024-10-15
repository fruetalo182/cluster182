# Install CLI
curl --proto '=https' --tlsv1.2 -sSfL https://run.linkerd.io/install-edge | sh
# Add it to the path
export PATH=$HOME/.linkerd2/bin:$PATH
# Check version
linkerd version
# Validate
linkerd check --pre
# Install onto cluster
linkerd install --crds | kubectl apply -f -
linkerd install | kubectl apply -f -
# Verify installation
linkerd check
# After installation inject linkerd proxy on the wished namespace

# Install viz
linkerd viz install | kubectl apply -f -
# Run viz
linkerd viz dashboard &
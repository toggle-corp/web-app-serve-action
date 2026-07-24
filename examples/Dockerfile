# Example fixture app used to exercise web-app-serve-action in CI.
# The stage name must match the action's `docker_target` input
# (default: web-app-serve).
FROM nginx:alpine AS web-app-serve
RUN printf '<h1>web-app-serve example</h1>' > /usr/share/nginx/html/index.html

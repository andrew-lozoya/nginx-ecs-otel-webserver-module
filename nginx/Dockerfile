#otel-webserver-module supports both stable(1.22.0) and mainline(1.23.1) for x86-64 platforms.
FROM --platform=amd64 nginx:1.23.1

# Replace the nginx.conf file with our own
COPY nginx.conf /etc/nginx/nginx.conf

# Copy in the Server configuration file
COPY default.conf /etc/nginx/conf.d/default.conf

# Define the version of the NGINX OTel module
ARG OPENTELEMETRY_CPP_VERSION=1.0.3

# Define the search path for shared libraries used when compiling and running NGINX
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/opentelemetry-webserver-sdk/sdk_lib/lib

#The latest OTel C++ web server module, otel-webserver-module supporting `NginxModuleOtelExporterOtlpHeaders` is only avaliable via GitHub Action Artifacts
#Ref: https://github.com/open-telemetry/opentelemetry-cpp-contrib/tree/main/instrumentation/otel-webserver-module#download-the-artifact-1
COPY opentelemetry-webserver-sdk-x64-linux.tgz 

RUN apt-get update \
  && apt-get install -y --no-install-recommends dumb-init unzip \
# 2. Extract the module files
  && tar xvfz /tmp/opentelemetry-webserver-sdk-x64-linux.tgz -C /opt \
  && rm -rf /tmp/opentelemetry-webserver-sdk-x64-linux.tgz \
# 3. Install and add the 'load_module' directive at the top of the main NGINX configuration file
  && /opt/opentelemetry-webserver-sdk/install.sh \
  && echo "load_module /opt/opentelemetry-webserver-sdk/WebServerModule/Nginx/1.23.1/ngx_http_opentelemetry_module.so;\n$(cat /etc/nginx/nginx.conf)" > /etc/nginx/nginx.conf

# 4. Copy in the configuration file for the NGINX OTel module
COPY opentelemetry_module.conf /opt/opentelemetry_module.conf

# Copyright 2019 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM gcr.io/cloud-builders/gcloud-slim

# Use ARG so that values can be overriden by user/cloudbuild
ARG TERRAFORM_VERSION=1.0.10
ARG TERRAFORM_VERSION_SHA256SUM=a221682fcc9cbd7fde22f305ead99b3ad49d8303f152e118edda086a2807716d
ARG TERRAFORM_VALIDATOR_RELEASE=v0.4.0

ENV ENV_TERRAFORM_VERSION=$TERRAFORM_VERSION
ENV ENV_TERRAFORM_VERSION_SHA256SUM=$TERRAFORM_VERSION_SHA256SUM
ENV ENV_TERRAFORM_VALIDATOR_RELEASE=$TERRAFORM_VALIDATOR_RELEASE

RUN uname -a 
RUN apt-get update && \
   /builder/google-cloud-sdk/bin/gcloud -q components install alpha beta && \
    apt-get -y install curl jq unzip git ca-certificates 
RUN curl https://releases.hashicorp.com/terraform/${ENV_TERRAFORM_VERSION}/terraform_${ENV_TERRAFORM_VERSION}_linux_amd64.zip \
      > terraform_linux_amd64.zip && \
    echo "${ENV_TERRAFORM_VERSION_SHA256SUM} terraform_linux_amd64.zip" > terraform_SHA256SUMS && \
    sha256sum -c terraform_SHA256SUMS --status && \
    unzip terraform_linux_amd64.zip -d /builder/terraform && \
    rm -f terraform_linux_amd64.zip 
RUN gsutil cp gs://terraform-validator/releases/${ENV_TERRAFORM_VALIDATOR_RELEASE}/terraform-validator-linux-amd64 /builder/terraform/terraform-validator && \
    chmod +x /builder/terraform/terraform-validator 
RUN apt-get --purge -y autoremove && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ENV PATH=/builder/terraform/:$PATH
COPY entrypoint.bash /builder/entrypoint.bash
RUN chmod +x /builder/entrypoint.bash
ENTRYPOINT ["/builder/entrypoint.bash"]

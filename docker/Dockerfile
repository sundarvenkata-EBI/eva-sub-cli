FROM python:3.10

ENV vcf_validator_version=0.9.4
ENV NXF_VER=22.10.6

WORKDIR /opt

# Install JAVA and Node
RUN apt update && apt install -y default-jdk nodejs npm

# Install VCF validator
RUN curl -LJo /usr/local/bin/vcf_validator  https://github.com/EBIvariation/vcf-validator/releases/download/v${vcf_validator_version}/vcf_validator_linux \
    && curl -LJo /usr/local/bin/vcf_assembly_checker  https://github.com/EBIvariation/vcf-validator/releases/download/v${vcf_validator_version}/vcf_assembly_checker_linux \
    && chmod 755 /usr/local/bin/vcf_assembly_checker /usr/local/bin/vcf_validator

# Install biovalidator and make it executable
# FIXME: remove hack to make executable when https://github.com/elixir-europe/biovalidator/pull/70 is merged
RUN git clone https://github.com/elixir-europe/biovalidator.git  \
    && cd biovalidator  \
    && bash -c "cat <(echo '#!/usr/bin/env node') <(cat src/biovalidator.js) > tmp"  \
    && mv tmp src/biovalidator.js \
    && chmod +x src/biovalidator.js \
    && sed -i 's/dist/src/' package.json \
    && npm install \
    && npm link

# Install nextflow
RUN curl -L "https://github.com/nextflow-io/nextflow/releases/download/v${NXF_VER}/nextflow-${NXF_VER}-all" | bash \
    && mv nextflow /usr/local/bin/

COPY . /opt/

RUN python setup.py install

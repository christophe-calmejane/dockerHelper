# Base our image from builder
FROM builder:ubuntu_21.10

# Install additional packages
RUN sudo apt-get update && sudo DEBIAN_FRONTEND=noninteractive apt-get -qq -y install \
	unzip \
	&& sudo rm -rf /var/lib/apt/lists/*

# Install SonarQube 'Scanner'
ARG ScanerVersion=4.7.0.2747
ARG ScannerZipName=sonar-scanner-cli-${ScanerVersion}-linux.zip
RUN wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/${ScannerZipName} -O ~/${ScannerZipName} \
	&& unzip ~/${ScannerZipName} -d ~ \
	&& rm -f ~/${ScannerZipName}

# Add it to the PATH
ENV PATH=$PATH:~/sonar-scanner-${ScanerVersion}-linux/bin

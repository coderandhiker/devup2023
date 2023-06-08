FROM rocker/r-ver:4.1.2

# [Option] Install zsh
ARG INSTALL_ZSH="true"
# [Option] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="false"

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get update \    
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    && apt-get install -y git

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \    
    && apt-get -y install libzip-dev \
    && apt-get -y install r-base \
    && apt-get -y install r-recommended r-cran-xml libgtk2.0-dev libxml2-dev \
    && apt-get -y install wajig \
    && apt-get -y install software-properties-common \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* 

# Update Ubuntu packages
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends libcurl4-openssl-dev \
    libssl-dev \
    libxml2-dev \
    python3-pip \
    libxt6

# Set up Python & R packages
RUN python3 -m pip install setuptools \ 
    && python3 -m pip --no-cache-dir install radian \
    && pip --disable-pip-version-check --no-cache-dir install pybryt \
    && pip --disable-pip-version-check --no-cache-dir install pylint \
    && pip --disable-pip-version-check --no-cache-dir install jupyter \
    && pip --disable-pip-version-check --no-cache-dir install datascience \
    && pip --disable-pip-version-check --no-cache-dir install otter-grader \
    && pip --disable-pip-version-check --no-cache-dir install numpy \
    && pip --disable-pip-version-check --no-cache-dir install pandas \
    && pip --disable-pip-version-check --no-cache-dir install scipy \
    && pip --disable-pip-version-check --no-cache-dir install folium>=0.9.1 \
    && pip --disable-pip-version-check --no-cache-dir install matplotlib \
    && pip --disable-pip-version-check --no-cache-dir install ipywidgets>=7.0.0 \
    && pip --disable-pip-version-check --no-cache-dir install bqplot \
    && pip --disable-pip-version-check --no-cache-dir install nbinteract>=0.0.12 \
    && pip --disable-pip-version-check --no-cache-dir install otter-grader \
    && pip --disable-pip-version-check --no-cache-dir install okpy \
    && pip --disable-pip-version-check --no-cache-dir install scikit-learn

# Set up the Rattle package for R from source
RUN echo | wajig addrepo ppa:marutter/rrutter4.0 \
    && echo | wajig addrepo ppa:c2d4u.team/c2d4u4.0+ \
    && wajig distupgrade -y \
    && wajig install libgtk2.0-dev r-base-dev r-cran-rattle -y

RUN install2.r --error --skipinstalled --ncpus -1 \
        devtools \
        languageserver \
        httpgd \
        tidyverse \
        tidymodels \
        statip \
        patchwork \
        paletteer \
        glmnet \
        randomForest \
        xgboost \
        here \
        doParallel \
        janitor \
        vip \
        ranger \
        palmerpenguins \
        skimr \
        nnet \
        kernlab \
        plotly \
        factoextra \
        cluster \
        ottr \
        rpart.plot \        
        RColorBrewer \
     && rm -rf /tmp/downloaded_packages

RUN R -e "install.packages(\"summarytools\")"
RUN R -e "library(ottr)"
RUN R -e "library(here)"
RUN R -e "library(languageserver)"
RUN R -e "install.packages(\"bitops\")"
RUN R -e "install.packages(\"XML\")"
RUN R -e "install.packages(\"rattle\", repos=\"https://rattle.togaware.com\", type=\"source\")"

# VSCode R Debugger dependency. Install the latest release version from GitHub without using GitHub API.
# See https://github.com/microsoft/vscode-dev-containers/issues/1032
RUN export TAG=$(git ls-remote --tags --refs --sort='version:refname' https://github.com/ManuelHentschel/vscDebugger v\* | tail -n 1 | cut --delimiter='/' --fields=3) \
    && Rscript -e "remotes::install_git('https://github.com/ManuelHentschel/vscDebugger.git', ref = '"${TAG}"', dependencies = FALSE)"

# R Session watcher settings.
# See more details: https://github.com/REditorSupport/vscode-R/wiki/R-Session-watcher
RUN echo 'source(file.path(Sys.getenv("HOME"), ".vscode-R", "init.R"))' >> ${R_HOME}/etc/Rprofile.site

RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends libzmq3-dev \
    && install2.r --error --skipinstalled --ncpus -1 IRkernel \
    && python3 -m pip --no-cache-dir install jupyter \
    && R --vanilla -s -e 'IRkernel::installspec(user = FALSE)'

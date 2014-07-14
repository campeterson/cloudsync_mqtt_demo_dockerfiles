# docker image for cloudsync client

FROM ubuntu

RUN apt-get -y install wget
RUN apt-apt -y install git

# install Ruby
RUN apt-get -y install build-essential zlib1g-dev libreadline-dev libssl-dev libcurl4-openssl-dev
RUN git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
RUN git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
RUN echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
RUN echo 'eval "$(rbenv init -)"' >> ~/.bash_profile ENV PATH /.rbenv/bin:/.rbenv/shims:$PATH
RUN echo PATH=$PATH
RUN rbenv init -
RUN rbenv install 2.1.2-p0 && rbenv global 2.1.2-p0

# never install a ruby gem docs
RUN echo "gem: --no-rdoc --no-ri" >> ~/.gemrc
 
# Install bundler and the "bundle" shim
RUN gem install bundler && rbenv rehash
  
# Checkout the cloudsync_mqtt_demo
RUN git clone -b master git://github.com/campeterson/cloudsync_mqtt_demo.git /cloudsync_mqtt_demo
   
# Optimization: Pre-run bundle install.
# It may be that some gems are installed that never get cleaned up,
# but this will make the subsequent CMD runs faster
RUN cd /cloudsync_mqtt_demo && bundle install
     
# Command to run at "docker run ..."
CMD if [ -z $BRANCH ]; then BRANCH=master; fi; \
    cd /cloudsync_mqtt_demo \
    && git checkout $BRANCH \
    && git pull \
    && bundle install \
    && bundle exec bundle exec ruby notification_worker.rb $CLOUDMQTT_URL $ACCOUNT_ID

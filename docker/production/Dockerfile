FROM phusion/passenger-ruby25:latest
MAINTAINER George Khromchenko "moxorama@gmail.com"

# Set correct environment variables.
ENV HOME /root

# Use baseimage-docker's init process.
CMD ["/sbin/my_init"]

# Expose Nginx HTTP service
EXPOSE 80

# Start Nginx / Passenger
RUN rm -f /etc/service/nginx/down

# Remove the default site
RUN rm /etc/nginx/sites-enabled/default

# Nginx App setup
ADD docker/production/app.nginx.conf /etc/nginx/sites-enabled/app.nginx.conf
ADD docker/production/rails-env.conf /etc/nginx/main.d/rails-env.conf
ADD docker/production/app-env.conf /etc/nginx/conf.d/00_app_env.conf

# Update for security reason
RUN apt-get update && apt-get upgrade -y -o Dpkg::Options::="--force-confold"

# Gem caching
WORKDIR /tmp
ADD Gemfile /tmp/
ADD Gemfile.lock /tmp/
RUN bundle install --jobs 20 --retry 5

# App setup
#
# This is your application setup steps, you should also do what you need to do here to make your 
# application working for production environment.
#
WORKDIR /var/www/app
ADD . /var/www/app
RUN rm -f config/database.yml
RUN mv config/database.yml.sample config/database.yml
RUN chown -R webdev:webdev /var/www/app
RUN rake assets:precompile RAILS_ENV=production

# Clean up APT when done.
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

FROM fluent/fluentd:v1.7-1

# Use root account to use apk
USER root

# below RUN includes plugin as examples loggly is not required
# you may customize including plugins as you wish
RUN apk add --no-cache --update --virtual .build-deps \
        sudo build-base ruby-dev \
 && sudo gem install fluent-plugin-loggly \
 && sudo gem sources --clear-all \
 && apk del .build-deps \
 && rm -rf /tmp/* /var/tmp/* /usr/lib/ruby/gems/*/cache/*.gem

COPY fluent.conf /fluentd/etc/
COPY entrypoint.sh /bin/

USER fluent

#FROM fluent/fluentd:v0.12-onbuild

# below RUN includes plugin as examples elasticsearch is not required
# you may customize including plugins as you wish

# RUN apk add --update --virtual .build-deps \
#         sudo build-base ruby-dev \
#  && sudo gem install \
#         fluent-plugin-loggly \
#  && sudo gem sources --clear-all \
#  && apk del .build-deps \
#  && rm -rf /var/cache/apk/* \
#            /home/fluent/.gem/ruby/2.4.0/cache/*.gem

FROM jakkbl/noid:latest
# based on Debian, Perl 5.34.1

ENV APP arknoid
ENV USER ark
ENV DIR /app/home/$USER

RUN groupadd -r $USER \
    && useradd --no-log-init -rm -g $USER -d $DIR -s /bin/bash $USER

COPY $APP original_shoulders $DIR/

WORKDIR $DIR

RUN mv $APP /usr/local/bin/$APP \
    && chmod 755 /usr/local/bin/$APP \
    && mkdir $DIR/minters \
    && chown -R $USER:$USER $DIR/minters

USER $USER

# give an arknoid command as docker args; no args command runs shell
CMD [ "/bin/bash" ]

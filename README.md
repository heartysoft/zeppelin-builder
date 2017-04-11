# zeppelin-builder

Docker container to help build Apache Zeppelin.
Uses Alpine

Contains:

* Node 6.10.2
* Git
* fontconfig
* Java 8u121
* Open JDK
* Maven 3.5.0


## Notes

To use:

* Base dockerfile off this one.
* Clone zeppelin repo into host machine.
* Remember to use non-root user.
* Might wish to take /home/mvnbuilder/custom-zeppelin/zeppelin-distribution/target/zeppelin-version-...tar.gz and put it into another (smaller) container for running in production.

Example:

```

FROM zeppelin-builder


RUN adduser -S mvnbuilder

RUN mkdir /home/mvnbuilder/custom-zeppelin
ADD ./custom-zeppelin /home/mvnbuilder/custom-zeppelin

RUN chown mvnbuilder /home/mvnbuilder/custom-zeppelin -R

USER mvnbuilder
WORKDIR /home/mvnbuilder/custom-zeppelin

RUN mvn clean
RUN mvn compile

RUN mvn package -DskipTests -Pscala-2.11 -Pspark-2.0 -Pshell -Pangular -Pmarkdown -Pcassandra  -Dhive -Dspark.version=2.0.2 -Pbuild-distr


# -Pscala-2.11 -Pyarn -Pspark-2.0 -Dhive -Dspark.version=2.0.2 -Pbuild-distr -Psparkr

EXPOSE 8080

RUN mkdir  /home/mvnbuilder/custom-zeppelin/logs 
RUN mkdir  /home/mvnbuilder/custom-zeppelin/run

CMD rm -rf ./logs/* && ./bin/zeppelin-daemon.sh  start && tail -f ./logs/*

```


FROM ubuntu
RUN apt update
RUN apt-get install -y openjdk-11-jdk
COPY Test.java .
RUN javac Test.java
CMD ["java", "Test"]

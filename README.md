This is a proof-of-concept Spring Boot Environment implementation for [Fission](http://fission.io).

# Prerequisites:

Download code from "https://github.com/spring-cloud/spring-cloud-function/tree/master/spring-cloud-function-compiler" and install it in local

```
$ git clone https://github.com/spring-cloud/spring-cloud-function.git

$ cd spring-cloud-function/spring-cloud-function-compiler/

$ mvn clean install
```


# Build the Environment Image

Change the `docker.image.prefix` property in `pom.xml` to your DockerHub username or organization.
Then execute the following commands:

```
$ ./mvnw clean package docker:build

$ docker push ravikalla/fission-spring-env
    Syntax: docker push [your-prefix]/fission-spring-env
```

# Create a Function

Precompile a Function such as "uppercase" (BUT name it "user" instead) as described in the Dynamic Compilation section of the `spring-cloud-function` [README](https://github.com/markfisher/spring-cloud-function#dynamic-compilation). Then copy `/tmp/function-registry/functions/user.fun` to the current directory.

# Deploy the Function

Start Fission as described in its [README](https://github.com/fission/fission).
Then execute the following commands:

```
$ fission env create --name spring --image ravikalla/fission-spring-env
    Syntax: fission env create --name spring --image [your-prefix]/fission-spring-env

$ fission function create --name uppercase --env spring --code user.fun

$ fission route create --method POST --url /uppercase --function uppercase
```

# Invoke the Function

```
$ curl -X POST -H "Content-Type: text/plain" -d "Hello World" http://$FISSION_ROUTER/uppercase
```



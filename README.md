# Azure Key Vault Config Source for MicroProfile Config API

This project contains a config source for the MicroProfile Config API. It allows for dependency injection of values from Azure Key Vault into MicroProfile-based microservices. Worked examples and further details will be published soon.

## Using the Azure Key Vault Config Source for MicroProfile Config API

Firstly, add the dependency on this library to your project. Below are examples of how to do this in Maven and Gradle:

**Maven:**

```xml
<dependency>
    <groupId>net.jonathangiles.microprofile.configsource.azure.keyvault</groupId>
    <artifactId>key-vault-config-source</artifactId>
    <version>0.0.2</version>
</dependency>
```

**Gradle:**

```gradle
compile group: 'net.jonathangiles.microprofile.configsource.azure.keyvault', name: 'key-vault-config-source', version: '0.0.2'
```

Once the dependency is added inside your MicroProfile-based application, you need to create a `microprofile-config.properties` file inside `src/main/resources/META-INF`. Inside this file, the following properties must be set based on the Azure Key Vault configuration you have created (if you haven't created an Azure Key Vault yet, refer to the [steps in my other repo](https://github.com/JonathanGiles/microprofile-on-azure/tree/master/keyvault-configsource)):

```properties
# taken from the service principal "appId" response
azure.keyvault.client.id=5292398e-XXXX-40ce-XXXX-d49fXXXX9e79

# taken from the service principal "password" response
azure.keyvault.client.key=9b217777-XXXX-4954-XXXX-deafXXXX790a

# is the key vault name specified in step two, e.g. 'microprofile-kv'
azure.keyvault.url=https://microprofile-kv.vault.azure.net
```

Save this file, and then we can move on to your REST endpoints. Again, the [keyvault-configsource sample app](https://github.com/JonathanGiles/microprofile-on-azure/tree/master/keyvault-configsource) has good reference code, but I have reproduced it below:

```java
import org.eclipse.microprofile.config.inject.ConfigProperty;

import javax.enterprise.context.ApplicationScoped;
import javax.inject.Inject;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;

import static javax.ws.rs.core.MediaType.TEXT_HTML;

@ApplicationScoped
@Path("/")
public class ConfigDemo {

    @Inject
    org.eclipse.microprofile.config.Config config;

    @Inject
    @ConfigProperty(name = "demo-key", defaultValue = "Unknown")
    String demoKeyValue;

    @GET
    @Path("config")
    @Produces(TEXT_HTML)
    public String info() {
        return "Welcome to the server! I know that the value for the key 'demo-key' is: '" + demoKeyValue
                + "'<br/><br/>"
                + "By the way, I can also look it up in a non-DI fashion: '" + config.getValue("demo-key", String.class) + "'";
    }
}
```
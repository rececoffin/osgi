# Leson 05 - Implementing a Service Provider
In this lesson, we implement a service provider to factor numbers.  


#### Step 1 - Open the module
* Navigate to the **trial-provider** module in your project. This module will hold the first implementation of our `Factorizer` service. It is called
 `trial` because that is the name of the factoring algorithm. 
 
#### Step 2 - Add the Apache math library.
We are not writing the factoring algorithm. We will use an Apache library to do the work. The math library keeps a list of prime numbers. The algorithm keeps trying to divide the input by the prime numbers until the input is fully decomposed.

Add this dependency to the module's POM file:

```xml
 <dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-math3</artifactId>
  <version>3.4.1</version>
 </dependency>
 ```

#### Step 3 - Create `trial` service provider class.
Up for a challenge? Try to write the service provider class.
 * Name the class `TrialDivisionProvider` 
 * Put it in a package named `trial`
 * Implement the `Factorizer` interface from the previous lesson
 * Use the static method `primeFactors` from the `org.apache.commons.math3.primes.Primes` class
 to factorize the input.
 
Compare your class to the sample solution.
 
#### Step 4 - Fix up (most, but not all of) the POM file
You are created a bundle. What else does your POM file need?
Update your POM file so that it produces a bundle. 
Read the list below to make sure you covered everything:

 * Add the `api` package from the previous lesson as a dependency
 * Add the `packaging` directive `bundle` 
 * Add the maven bundle plugin
 
#### Step 5 - Embed the math library in the bundle file
* Read about bundle [imports, exports and embedded artifacts](imports-exports-and-embedding.md).
* Configure the maven bundle plugin to embed the Apache math library. Try to complete this 
step without looking at the sample solution. After working on it for a 2 minutes, compare
what you write with the sample solution.

#### Step 6 - Reigster the service provider with OSGi
* Create a blueprint XML file for this Maven module.
* This bit of XML is enough to create an instance of the `TrialDivisionProvider` and register it:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0">
    <!--OSGi is going to look for an interface in the runtime environment with the provided package/class-->
    <!--name. It will then create a (single) instance of the TrialDivisionProvider class and "advertise"-->
    <!--it as provider of the Factorizer service. The provided service can be referred to by its ID, "trial".-->
    <service id="trial" interface="api.Factorizer">
        <bean class="trial.TrialDivisionProvider"/>
    </service>
</blueprint>
```
In English this reads: I want to register a service provider and give it the ID `trial`. 
My service provider implements the `api.Factorizer` service. 

#### Step 7 - Build, install, and test the `trial` service provider
* Remember, trial bundle imports the Java package `api`. Be sure the `api` bundle is loaded 
into the OSGi runtime first.
* Use the Karaf `services` command to verify the bundle provides the `api.Factorizer` service. 
Look for something like this:

```trial provides:
[api.Factorizer] 
```

#### Step 7.5 - Add some extra information to the service registration. 
Create a key-value pair. Make the key `name` and the value `trial-division`. Add
the following to the `service` element in the blueprint file:

```xml
<service-properties>
            <entry key="name" value="trial-division"/>
</service-properties>
```

Check it with the command `services -p trial`.


* Test the service from the Karaf command line:

```text
      (service:get api.Factorizer) getFactors 42
```

#### Step 8 - What if?
* What error would you get if you tried to load this bundle without embedding the library? Remove
 the embed directive from the POM file, rebuild and reload. Did you see what you expected?
* Now, uninstall the `trial` bundle. Then uninstall the `api` bundle.
* What error would you get if you now tried to install the run the `trial` bundle?
* Try it to confirm the error message.

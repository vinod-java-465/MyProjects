/*
 *
 * Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with
 * the License. You may obtain a copy of the License at
 *
 * http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on
 * an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the
 * specific language governing permissions and limitations under the License.
 *
 * Copyright 2013-2016 the original author or authors.
 */

package com.example.FF4J_Test.config;

import org.ff4j.FF4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;



/**
 * Definition of FF4j Bean. This definition should be done not only
 * in the backend with web console and rest API but also in your microservices.
 */
@Configuration
public class FF4jConfig {
    
    
	@SuppressWarnings("deprecation")
	@Bean
    public FF4j getFF4j() {
        /*
         * 0. Create bean needed for your stores based on the technology you want to use
         * 
         * Because I pick in memory store, I want to load init values from a file I can use 
         * YAML, XML or properties File. This is optional you can start with empty store
         * or even create feature programmatically.
         */
//        InputStream dataSet = FF4j.class.getClassLoader().getResourceAsStream("NewFile.xml");
//        // We imported ff4j-config-yaml to have this
//        FF4jConfiguration initConfig = new YamlParser().parseConfigurationFile(dataSet);
//        LOGGER.info("Default features have been loaded {}", initConfig.getFeatures().keySet());
//        
//        // 1. Define the store you want for Feature, Properties, Audit among 20 tech
//        FeatureStore  featureStore  = new InMemoryFeatureStore(initConfig);
//        PropertyStore propertyStore = new InMemoryPropertyStore(initConfig);
//        EventRepository logsAudit   = new InMemoryEventRepository();
//        
//        // 2. Build FF4j
//        FF4j ff4jBean = new FF4j();
//        ff4jBean.setPropertiesStore(propertyStore);
//        ff4jBean.setFeatureStore(featureStore);
//        ff4jBean.setEventRepository(logsAudit);
//        
//        // 3. Complete setup
//        ff4jBean.setEnableAudit(true);
//        ff4jBean.setAutocreate(true);
//        return ff4jBean;
//        
//        // you can do the same in 1 line
        return new FF4j("FF4JFeatures.xml").audit(true).autoCreate(true);
    }
}

-------------------------------------------------------------------------------------------------------------------------------------------------------------


package com.example.FF4J_Test.config;

import org.ff4j.FF4j;
import org.ff4j.spring.boot.web.api.config.EnableFF4jSwagger;
import org.ff4j.web.FF4jDispatcherServlet;
import org.springframework.boot.autoconfigure.AutoConfigureAfter;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import springfox.documentation.swagger2.annotations.EnableSwagger2;

/**
 * Sample configuration for WebConsole and RestAPI
 * 
 * REST API:
 *  To enable REST API you need to provide annotation @EnableFF4jSwagger. Once done
 *  you can use the endpoint api/ff4j/.
 *  
 * WEB CONSOLE:
 *  the web console will be available at /ff4j-web-console/home/
 */
@Configuration
@ConditionalOnClass({FF4jDispatcherServlet.class})
@AutoConfigureAfter(FF4jConfig.class)
public class FF4jWebConsoleConfiguration extends SpringBootServletInitializer {
    
    /**
     * Definition of the servlet for web console
     */
    @Bean
    @ConditionalOnMissingBean
    public FF4jDispatcherServlet getFF4jDispatcherServlet(FF4j ff4j) {
        FF4jDispatcherServlet ff4jConsoleServlet = new FF4jDispatcherServlet();
        ff4jConsoleServlet.setFf4j(ff4j);
        return ff4jConsoleServlet;
    }
    
    /**
     * Mapping from FF4j to the endpoint you want
     */
    @Bean
    @SuppressWarnings({"rawtypes","unchecked"})
    public ServletRegistrationBean ff4jDispatcherServletRegistrationBean(FF4jDispatcherServlet ff4jDispatcherServlet) {
        return new ServletRegistrationBean(ff4jDispatcherServlet, "/ff4j-web-console/*");
    }
    
    
}
------------------------------------------------------------------------------------------------------------------------------------------
<?xml version="1.0" encoding="UTF-8" ?>
<ff4j xmlns="http://www.ff4j.org/schema/ff4j"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.ff4j.org/schema/ff4j http://ff4j.org/schema/ff4j-1.4.0.xsd">
    <features>
        <feature uid="Vinod" enable="true" description="name of P2 grade employeee."></feature>
         <feature uid="Nani" enable="true" description="name of P2 grade employeee."></feature>
            </features>
</ff4j>

---------------------------------------------------------------------------------------------------------------------------------------------

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.6.3</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>FeatureToggle</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>RAD</name>
	<description>FF4J_Testproject for Spring Boot</description>
<properties>
		<java.version>1.8</java.version>
		<ff4j.version>1.8.6</ff4j.version>
		<maven-jar-plugin.version>3.1.1</maven-jar-plugin.version>
	</properties>
	<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		
		<dependency>
			<groupId>org.ff4j</groupId>
			<artifactId>ff4j-spring-boot-starter</artifactId>
			<version>${ff4j.version}</version>
		</dependency>

		<!-- FF4j in memory -->
		<dependency>
			<groupId>org.ff4j</groupId>
			<artifactId>ff4j-web</artifactId>
			<version>${ff4j.version}</version>
		</dependency>

	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<!-- <configuration>
					<layers>
						<enabled>true</enabled>
					</layers>
				</configuration> -->
			</plugin>
		</plugins>
	</build>


</project>

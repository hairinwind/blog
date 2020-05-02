## Java functional refactoring
We have this code in project and we found it was quite slow the first time to run it. We guess the "createMarshaller" is expensive. We decide to add the spring stopwatch around it to see how long it takes.  
```
    public String xmlStringCleaner(Object object, Type bodyType) throws JAXBException {
        Marshaller marshaller = jaxbContextFactory.createMarshaller((Class<?>) bodyType);
        StringWriter stringWriter = new StringWriter();
        marshaller.marshal(object, stringWriter);
        return stringWriter.toString().replaceAll("<((?!na=\\\"true\\\")[^>])*/>", "");
    }
```

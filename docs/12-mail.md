# Mail

Modul untuk mengirim email.

## Bean

``` java
@Bean
protected MailHandler mailHandler(TaskHandler taskHandler) {
    return new MailHandlerImpl()
    .setTaskHandler(taskHandler)
    .setMailProperties(appProperties.getMail());
}
```

## Contoh Properties

``` md
mail:
    host: smtp.gmail.com
    port: 587
    username: "<username>"
    password: "<password>"
    properties:
        "mail.smtp.host": "smtp.gmail.com"
        "mail.smtp.ssl.trust": "smtp.gmail.com"
        "mail.smtp.port": "587"
        "mail.smtp.auth": "true"
        "mail.smtp.starttls.enable": "true"
        "mail.imap.ssl.enable": "true"
        "mail.transport.protocol": "smtp"
        "mail.debug": "true"
        "mail.smtp.ssl.protocols": "TLSv1.2"
```

##

### [Index](./index.md)

# Report

Membuat report menggunakan library [Jasper Report](https://www.jaspersoft.com/).

``` java
public interface ReportHandler {
	List<ReportType> getReportTypes();
	JasperPrint createPrint(ReportInput input) throws JRException;
	ReportResult createReport(ReportInput input, boolean useExportManager) throws Exception;
	ReportResult createReport(ReportInput input) throws Exception;
	void exportReport(ReportInput input, OutputStream outputStream) throws Exception;
}
```

## Bean

``` java
@Bean
protected ReportHandler reportHandler() {
    return new ReportHandlerImpl();
}
```

## Type

* PDF
* XLS
* XLSX
* RTF
* DOCX
* PPTX
* CSV
* XML
* JSON
* HTML

##

### [Index](./index.md)

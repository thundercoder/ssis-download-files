# files-downloader.process

Integration Services used to download files, in this example I created it only for pdf extension but you can set it for whatever type dynamically using variables inside the package.

In this project I use the following tools:

1.  SQL Integration Services
2.  SQL SERVER 2008 R2 (You can migrate it to 2014, it's not a big impact)

## Installation

Download to your project directory, add it, and commit.

```sh
$ git clone https://github.com/thundercoder/ssis-download-files.git <<local-folder>>
```

## Usage

After downloading the project you need to set all the components and varibles enviroment, so do the following things:

- 1.	Variables:
  - 1.1 Customers is the dataset from your query made (SQL SERVER in this case).	
  
  - 1.2 CustomerIdentification and CustomerName there are used to be filled when the forEach is reading the dataset (Variable Customers) of the SQL Task Component.
  
  - 1.3 FilesDirectory, as your name say it, it's the directory where you're gonna save the file you download.
  
  - 1.4 Period is a parameter specifying the document's period to be sent through url.
  
  - 1.5 ServerRequested is the server where you will send the request

- 2. Components
  
  - 2.1 The first component you will see is Foreach Loop Container with a File Task inside. It's gonna remove all files of your FileDirectory Variable, it has *.* include in file extensions to erase all files.
  
  - 2.2 SQL TASK to fille the Customers variable.
  
  - 2.3 Foreach Loop Container to go through the Customer variable mapping the others variables: CustomerIdentification and CustomerName to be send via url to the request.
  
  - 2.4 Script Task in charge to send the request via url. When you open this component, click to 'Edit Script', you will see the following code that is in charge to download the file (Configure it! xD all the variable are clear and is clear to understand):

```
' Get the unmanaged connection object, from the connection manager called "HTTP Connection Manager"
        Dim nativeObject As Object = Dts.Connections("HTTP Connection Manager").AcquireConnection(Nothing)

        ' Create a new HTTP client connection
        Dim connection As New HttpClientConnection(nativeObject)

        connection.ServerURL = "http://" & Dts.Variables("User::ServerRequested").Value & "/ReportServer/Pages/Report.aspx?%2fComprobantesDB%2frptCargosBancariosAut&rs:Command=Render&rs:Format=PDF&pCliID=" & Dts.Variables("User::CustomerIdentification").Value & "&pNCF=A0100100101428359&Guid=4497db87-d30c-4403-82a7-d6f24f4f1dd9&pCliNombre=" & Dts.Variables("User::CustomerName").Value & "&Pertenece606=0&Periodo=" & Dts.Variables("User::Period").Value

        ' Download the file #1
        ' Save the file from the connection manager to the local path specified
        Dim filename As String = "C:\Temp\" & Dts.Variables("User::CustomerIdentification").Value & ".pdf"
        connection.DownloadFile("C:\Temp\" & Dts.Variables("User::CustomerIdentification").Value & ".pdf", True)
        '"http://intranetdb/ReportServer/Pages/Report.aspx?%2fComprobantesDB%2frptCargosBancariosAut&rs:Command=Render&rs:Format=PDF&pCliID=" & Dts.Variables("User::ClienteIdentificacion").Value & "&pNCF=A0100100101428359&Guid=4497db87-d30c-4403-82a7-d6f24f4f1dd9&pCliNombre=" & Dts.Variables("User::ClienteNombre").Value & "&Pertenece606=0&Periodo=012018"
        ' Confirm file is there
        'If File.Exists(filename) Then
        'MessageBox.Show(String.Format("File {0} has been downloaded.", filename))
        'End If


        ' Download the file #2
        ' Read the text file straight into memory
        Dim buffer As Byte() = connection.DownloadData()
        Dim data As String = Encoding.ASCII.GetString(buffer)

        Dts.TaskResult = ScriptResults.Success
```

## Support

Please open an issue to receive support for this project.

## Contributing

Fork the project, create a new branch, make your changes, and open a pull request.
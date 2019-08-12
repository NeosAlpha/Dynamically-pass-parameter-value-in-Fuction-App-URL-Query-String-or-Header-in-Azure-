# Dynamically pass parameter value in Fuction App URL Query String or Header in Azure  
Published by NeosAlpha Technologies 


# Function App

We are creating Function App with HTTP Trigger in Visual Studio and configure Azure Blob storage connection string. Here we are getting file name dynamically from Fuction App URL query string or Header and Read data from CSV file and Transform data into JSON format.

Here we can call Function App like : {functionappurl}?filename=test.csv .
Code will be like:
      
        [FunctionName("CSVtoJSON")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Function, "get", "post", Route =null)] HttpRequest req
        , [Blob("hotfile/{query.filename}", FileAccess.Read, Connection = "AzureWebJobsStorage")]Stream myBlob, TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            string name = req.Query["name"];
            var json = Convert(myBlob);
            return (ActionResult)new OkObjectResult(json);
           
        }
        public static string Convert(Stream blob)
        {
            // Properties per = new Properties();
            //List<Properties> records = new List<Properties>();
            var sReader = new StreamReader(blob);
            var csv = new CsvReader(sReader);

            csv.Read();
            csv.ReadHeader();

            //*** To add into list without customization ***//
            var csvRecords = csv.GetRecords<object>().ToList();

            return JsonConvert.SerializeObject(csvRecords);
        }

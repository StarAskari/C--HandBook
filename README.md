# C--HandBook


        public ActionResult PrintGrp(InputPrintGrpModel data)
        {
            UiActionResualt uiActionResualt = new UiActionResualt(null);
            uiActionResualt.Success = false;
            var lstfileAddress = new List<string>();
            try
            {
                Mapper.Initialize(cnf => cnf.CreateMap<InputPrintGrpModel, InputPrintGrp>());
                InputPrintGrp inputPrintGrp = Mapper.Map<InputPrintModel, InputPrintGrp>(data);

                var lstEntity = yourOwnService.YourServiceMethod1(inputPrintGrpBimename);

                foreach (var entity in lstEntity.OrderBy(o => o.ID).ToList())
                {
                    var res1 = youtOwnService.yourOwnGetMethod1(bimename.ID);
                    var fileAddress = "";
                    if (Statement)
                    {
                        var YourEntity =YourMethod();
                        fileAddress = GetPrint(YourEntity);
                    }
                  
                    lstfileAddress.Add(fileAddress);
                    //var bimenameOmreArmaghavanReport = bimeNameOmrEnfService.GetBimenameOmreArmaghavanReportBybimeOmrEnfId(id);
                }
                var res = MergePDF(lstfileAddress);
                uiActionResualt.Data = res;
                uiActionResualt.Success = true;


            }
            catch (Exception exp)
            {
                uiActionResualt.Success = false;
                uiActionResualt = GetUiActionResult(exp);
            }

            return new JsonNetResult { Data = uiActionResualt };
        }


        public  string MergePDF(List<string> files)
        {
            string[] fileArray = new string[files.Count()];
            int count = 0;
            foreach (var file in files)
            {
               var fnew= Path.Combine(System.Web.Hosting.HostingEnvironment.MapPath("~/Content/Download"), file);
                fileArray[count] = fnew;
                count++;
            }
           

            PdfReader reader = null;
            Document sourceDocument = null;
            PdfCopy pdfCopyProvider = null;
            PdfImportedPage importedPage;
            var fileName = Guid.NewGuid().ToString();
            var outputPdfPath = System.Web.Hosting.HostingEnvironment.MapPath("~/Content/Download/" + fileName + ".pdf");
             sourceDocument = new Document();
            pdfCopyProvider = new PdfCopy(sourceDocument, new System.IO.FileStream(outputPdfPath, System.IO.FileMode.Create));

            //output file Open  
            sourceDocument.Open();


            //files list wise Loop  
            for (int f = 0; f <= fileArray.Length - 1; f++)
            {
                int pages = TotalPageCount(fileArray[f]);

                reader = new PdfReader(fileArray[f]);
                //Add pages in new file  
                for (int i = 1; i <= pages; i++)
                {
                    importedPage = pdfCopyProvider.GetImportedPage(reader, i);
                    pdfCopyProvider.AddPage(importedPage);
                }

                reader.Close();
            }
            //save the output file  
            sourceDocument.Close();
            return outputPdfPath;
        }

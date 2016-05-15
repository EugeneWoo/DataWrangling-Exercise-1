### DataWrangling-Exercise-1
##### #read ‘refine_original.csv’ into R 
refine_original<-tbl_df(read.csv(file.choose(),header=T))

##### convert ‘company’ column to lower case
co<-refine_original$company
co<-tolower(co)

##### clean ‘company’ column
co<-gsub('.*lip.*$','philips',co)
co<-gsub('phillps','philips',co)
co<-gsub('^ak.*','akzo',co)
co<-gsub('^van.*','van houten',co)
co<-gsub('^unil.*','unilever',co)

##### substituting cleaned column for the original in 'refine1'
refine1<-cbind(co,refine_original)
refine1$company<-NULL
colnames(refine1)[1]<-‘company’

##### separate product code and number into respective columns under new dataframe 'refine2'
refine2<-refine1 %>% separate(Product.code...number,c('product_code','product_number'),sep='-')

##### add new column 'product_cat’ under new datagrame 'refine3'
pcode<-refine2$product_code
pcat<-ifelse(pcode=='p','Smartphone',ifelse(pcode=='v','TV',ifelse(pcode=='x','Laptop','Tablet')))

refine3<-cbind(refine2,product_cat=pcat)

##### add full address for geocoding under new dataframe 'refine4'
refine4<-refine3 %>% unite('full_address',address,city,country,sep=', ')

##### create four binary columns for company
company_philips<-ifelse(co=='philips',1,0)
company_akzo<-ifelse(co=='akzo',1,0)
company_van_houten<-ifelse(co=='van houten',1,0)
company_unilever<-ifelse(co=='unilever',1,0)

##### adding the new columns under new dataframe 'refine5'
refine5<-cbind(refine4,company_philips,company_akzo,company_van_houten,company_unilever)

##### additional step: ensuring the company binary columns sum to 25
sum(company_akzo,company_philips,company_unilever,company_van_houten)

##### create four binary columns for product category
product_smartphone<-ifelse(pcat=='Smartphone',1,0)
product_tv<-ifelse(pcat=='TV',1,0)
product_laptop<-ifelse(pcat=='Laptop',1,0)
product_tablet<-ifelse(pcat=='Tablet',1,0)

##### adding the new columns under new dataframe 'refine6'
refine6<-cbind(refine5,product_smartphone,product_tv,product_laptop,product_tablet)


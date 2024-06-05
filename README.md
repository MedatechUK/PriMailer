# PriMailer
 Priority HTML emaila
# Priority sending emails plan:

    -First thing to do is to come up with types of HTML template messages,
      In our case there are 3 types for 3 statuses of online orderes places by customer: 
        - Pending Approval
        - Confirmed
        - Shipped

    -In order to crate HTML template we are going to need few things which each of templates consits of:
        - Static data(text) which most likely differs from template to template
        - Dynamic data(name/order number/order date) which we SELECT from client's Database
        - Static images such as company logo which stays the same on each of templates
        - Dynamic images such as order statuses

    -Create procedure/choose from existing procedures one coresponding to our purposes and write a SQL statement
    which would SELECT all required data and put it into declared above variables
    those variables would be passed into relevant variables of our HTML template 

    -Create another procedure for storing out HTML templates created earlier



    
## Step_1: Uploading images

    1. Find all the images needed and upload them to the virtual folder on the client's IIS server:
        - Open IIS manager --> Sites --> Default Web Sits --> #find or create folder for storing images# --> primail --> images
        ![Image uploading to the IIS](./upload_images.png)

    2. Create a link for aech images you stored by simply adding them into the WEB path of client's server:
        ![Image links making](./making_image_links.png)

    3. Save all the links for each image somwhere as you will need them further to pass into HTML templates 
        -in our case there are <P2> and <P5> coresponding to order number and customer name

    


## Step_2: Creating HTML templates

    1. Build your HTML templates in regards to client's requirement
        - in our case there are 3 templates for different online order statuses
        such as "Pending Approval", "Confirmed", "Shipped"

    2. Define what data would be dynamic (taken from client's database) and highlight it as we will need to 
        swap this data for parameters(variables) to which we asign SELECTed fields from client's DB
        ![highlighting dynamic data](./highlighting_dynamicdata.png)




## Step_3: Creating Procedure for holding HTMLS

    1. So, go to procedure generator, and create procedure which only purpose will be to store our HTMLs
        - in our case it's called ZTMD_EMAILS with "Templates" description 
          ![creating procedure for holding HTML templates](./procedure_template.png)

        - 1.1. Go to sublevel form "Procedure Steps" and create a SQLI entity
          ![Creatinmg SQLIentity](./SQLIentity.png)

        - 1.2. Go to sub form "Step Query" and create just 1 line with comments tags (/* */)
          the reason we are doing that is to get to the procedure messages which is the sub form 
          to SQLI entity only! But beacause we don't need any SQL statements here, we leave it as a comment string as
          we still need to put something in this form to get to its sub form
          ![Comment string](./Comment_string_for_SQLI.png)

        - 1.3. Go to sub form "Procedure Messages". Here we are going to load our HTML templates.
          Here we create records and name them correspondingly with our HTML templates names 
          - Very important is to leave the first line !!! EMPTY !!! because:
          "the blank step is required to tell the sendmail command that it's using the temp file as the body, rather than as an attachment"
          otherwise our templates will not be shown as actual email format page, it would be considered as attachment(pdf, docx)
          ![loading htmls](./loading_htmls.png)
       
       - 1.4. Go to sub form "Procedure Messages (content)" for each of HTML and insert our HTMLs accordingly.
          - Make sure your HTML tags and it's contents are all aligned by left side before you paste it, 
          there should be no white spaces or new line symbols.
          - To do that go to where your HTML was written(VScode in our case) --> open HTML file --> press "ctrl + H"
          and make 3 steps to remove spare symbols:
            - replace all "\n[ ]+" with ""
            - replace all "<" with "\n<"
            - replace all "\n\n" with "\n"



    
##Step_4: Creating Procedure for Sending Emails

    1. Getting values we are going to include in our HTML templates:

        1.1. Go to procedure generator, and create procedure which will select all dynamic data from DB and send it to customer
         - (ZTMD_MAILTEST in our case)
        ![send emails procedure](./procedure_sendmails.png)
        
        1.2. Go to sub form "Procedure Steps" and create a SQLI entity
        ![SQLI_entity](./SQLI_emails.png)
        
        1.3. Go to sub form "Step Query" pres F6 and open code editor:
        - Okay, Lets split this code in few sections for easier understanding:

        1.4. SELECT statement
        ![select statement](./SELECT.png)
        - Just selecting fields that we spoke about earlier as of dynamic data
        
        1.5. Declaring Cursor and looping through our records in order to assign result values to so-called variables
        for each of lines in response table
        ![Fetching cursor into corresponding variables](./fetching_cursor.png)

        1.6. Selecting these variables into new variables(parameters) used as a reserved variables in procedures.
         -At this point we could have fetched our cursor straight into :PAR variables at the first place
        ![Selecting previous variables into :PAR variables](./PAR_variables.png)


    2. Email-Sending steps:

        2.1. Declaring few self-explanatory variables such as :_REPLYTOEMAIL, :_Subject, :sendto
        ![Setting To/From emails](./email_variables.png)
        
        2.2. Choosing HTML template by MSG number we defined earlier (##Step_3: Creating Procedure for holding HTMLS[1.3.])
        -As we defined  MSG number against each of our HTML we just take this number and assign it to a :ENTMSG variable,
        also we assign (:ENT = 'ZTMD_EMAILS') name of procedure to entity variable and 
        :ENTTYPE = 'P' as Procedure type for our ZTMD_EMAILS procedure
        ![Declaring entity variables](./Entity_vars.png.png)

        2.3. 
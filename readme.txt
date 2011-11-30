================== ML Crawler ==================
This crawler wrapes the functionality provided by open-source crawler.
It takes crawling instructions in xml format and returns returns the 
crawling results in XML format for ML Ingestion
		*** For internal use only ***

Steps:
1. Unzip the folder 
2. Folder Structure:
 	a) ML-Crawler.jar: Jar file which wrapes the functionality provided 
 	   by the open source crawler. 
 	b) configurations: 
 	   	setup files which should not be touched 
 	     i)  functions.xml
 	     ii) xquery.xml
 	     iii) crawler.xml
 	    Along with the setup files mentioned above, this folder is also the placeholder for the crawling instructions 
 		This folder is the place-holder for the crawling-instruction (e.g. sample-setup.xml).
 		Crawler required the following items to be configured prior to executing, for specifying the items, you would need to do a 'view-source' on the html page
 		
 		<!-- ======== Defining the crawling premise =========== -->
 		<?xml version="1.0" encoding="UTF-8"?>
			<config charset="ISO-8859-1">
		    <include path="..//setup//functions.xml"/>
    	 	<!-- Crawling definition begins here -->
    		<var-def name="products">    
        		<call name="download-multipage-list">
            		<call-param name="pageUrl">http://topsy.com/s?q=Nyquil</call-param> <!-- Define the URL to be crawled  -->
            		<call-param name="nextXPath">//a[starts-with(., 'next')]/@href</call-param> <!-- Define the HTML pattern to determine the next page URL for paginated content  -->
            		<call-param name="itemXPath">//span[@class="twitter-post-text translatable language-en"]</call-param> <!-- Define the HTML pattern to capture desired content  -->
            		<call-param name="maxloops">10</call-param><!-- Looping variable to define the number of iterations crawler should perform  -->
        		</call>
    		</var-def>
 		<!-- ========== Building the result files =============== -->
 		<!-- iterates over all crawled items and extract desired data -->
    	<file action="write" path="nyquil.xml" charset="UTF-8"> <!-- Set the output filename  -->
       
       	<!-- build the basic doc structure -->
       	<![CDATA[ <reviews> ]]> 
        <loop item="item" index="i">
            <list><var name="products"/></list>
            <body>
                <xquery>
                    <xq-param name="item" type="node()"><var name="item"/></xq-param>
                    <xq-expression><![CDATA[
                            declare variable $item as node() external;
                             <!-- Specify the HTML pattern to capture the text  -->
                            let $desc := data($item//*[@class='twitter-post-text translatable language-en'])
                            let $user := data($item//*[@class='author-name'])
                                return
                                    <review>
                                        <comment>{normalize-space($desc)}</comment>
										<reviewer>{normalize-space($user)}</reviewer>                                        
                                    </review>
                    ]]></xq-expression>
                </xquery>
            </body>
        </loop>
        <![CDATA[ </reviews> ]]>
    	</file>
		</config>
 3. Once the setup file is place in the folder, open terminal/command prompt and run the following command
    	java -jar ML-Crawler.jar
    
 4. After execution, you will have the results stored in the folder that you defined in the xml above.    
		
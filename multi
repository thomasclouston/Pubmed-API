import xml.etree.ElementTree as ET
import pandas as pd
import requests

part1="readmission"
part2=["Jaundice","variceal bleeding","variceal bleed", "ascites", "spontaneous bacterial peritonitis", "SBP" , "Hepatic encephalopathy" , "H.E." , "cirrhosis"]
#empty lists
search_results_for_each=[]
PMID_for_papers_results=[]
#get request
for searchterm in part2:
    response= requests.get("https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&term=+"+ part1+ "+AND+" + searchterm +'&retmax=100000')
    root=ET.fromstring(response.content)
    i=0
    #logic and storage

    for x in root.iter('Count'):
        if i<1:
          i=i+1
          search_results_for_each.append(x.text)

    for Information in root:
        for Id in Information.iter('Id'):
            IDs=Id.text
            if IDs not in PMID_for_papers_results:
                PMID_for_papers_results.append(IDs)
    print("Term Done")

#listcreation
articletitle=[]
journaltitle=[]
pubyear=[]
pubmonth=[]
abstract_list=[]


for PMIDs in PMID_for_papers_results:
    response= requests.get("https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&id=" + PMIDs + "&retmode=xml")
    root= ET.fromstring(response.content)
    x = str()
    for Information in root:
        #finds article and journal title
        for title in Information.iter('ArticleTitle'):
            articletitle.append(title.text)
        for info in Information.iter('Journal'):
            for jtitle in info.iter('Title'):
                journaltitle.append(jtitle.text)
        #finds journal publihication date
            for information in info.iter('JournalIssue'):
                for furtherinfo in information.iter('PubDate'):
                    for finalinfo in furtherinfo.iter('Year'):
                             pubyear.append(int(finalinfo.text))
                    for finalinfo in furtherinfo.iter('Month'):
                            pubmonth.append(finalinfo.text)
        #finds abstract
        for abstracts in Information.iter('Abstract'):
            for abstract in abstracts.iter('AbstractText'):
                    if abstract.text != None:
                        x = x + " " + (abstract.text)
                    else:
                        x = "Abstract Error"

        abstract_list.append(x)
        print("Abstract done")

#datadisplay

requested_information_list=[]
requested_information_list.append(PMID_for_papers_results)
requested_information_list.append(articletitle)
requested_information_list.append(journaltitle)
requested_information_list.append(pubyear)
requested_information_list.append(pubmonth)
requested_information_list.append(abstract_list)

#create dataframe
newDF=pd.DataFrame(requested_information_list)
df=newDF.transpose()
df.to_excel("papers.xlsx")

print("Total number of papers (excluding repeats )"+ str(len(PMID_for_papers_results)))
print("Total number of search results "+ str(sum(int(x) for x in search_results_for_each)))
print("search results for each term ")
print(search_results_for_each)

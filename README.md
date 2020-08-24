# IMDB-Project
Webscrapping

import numpy as np
import pandas as pd
import re
import requests
from bs4 import BeautifulSoup

URL = 'https://www.imdb.com/search/title/?genres=adventure&sort=user_rating,desc&title_type=feature&num_votes=25000,&pf_rd_m=A2FGELUUNOQJNL&pf_rd_p=5aab685f-35eb-40f3-95f7-c53f09d542c3&pf_rd_r=0TCA6YJXXHF4970T9ACZ&pf_rd_s=right-6&pf_rd_t=15506&pf_rd_i=top&ref_=chttp_gnr_2'

page = requests.get(URL)
pagecontent = page.text
pagecontent

soup = BeautifulSoup(pagecontent)
soup
# Extracting ratings for all movies:
imdb_rating = soup.find_all('div', class_ = 'inline-block ratings-imdb-rating')
imdb_rating[0]
imdb_rating[0].text.strip() 
print(list(map(lambda x: x.text.strip(), imdb_rating)))
# Extract the metascore:
imdb_metascore = soup.find_all('div', class_= 'inline-block ratings-metascore')
imdb_metascore[10]
imdb_metascore[10].text.strip().split()[0]
print(list(map(lambda x: x.text.strip().split()[0], imdb_metascore)))    # Metascores for all the movies in first page.
# Now extract the each movie info:
movie_content = soup.find_all('div', class_= 'lister-item mode-advanced')

meta_list = []    # Create an empty list

for x in movie_content:
    metascore = x.find_all('div', class_= 'inline-block ratings-metascore')
    if len(metascore) ==1:
        meta_list.append(metascore[0].text.strip().split()[0])
    else:
        meta_list.append(np.NaN)  
        
print(len(meta_list))   
print(meta_list)

# Extract movie title:
for x in movie_content:
    title = x.find_all('h3', class_ ='lister-item-header')[0].text.strip().replace('\n', '')
    print(title)
    
 # Extracting the required information:
 title = []
year = []
rank =[]
certificate =[]
runtime =[]
genre = []
rating = []
metascore = []
director = []
stars = []
votes = []
gross = []

for container in movie_content:  

    # Rank, Title and Year
    movie_title = container.find_all('h3', class_ ='lister-item-header')[0].text.strip().replace('\n', '')
   #print(movie_title)
    rank_regex = re.compile('^\d+')             # This creates the pattern object
    rk = rank_regex.findall(movie_title)
    
    
    title_regex = re.compile('^\d+\.(.*)\(')   
    tt = title_regex.findall(movie_title)
    
    
    year_regex = re.compile('\((\d+)\)')
    yr= year_regex.findall(movie_title)
    
    if len(rk) ==1:
        rank.append(rk[0])
    else:
        rank.append(np.NaN)
    
    if len(tt) ==1:
        title.append(tt[0])
    else:
        title.append(np.NaN)
    
    if len(yr) ==1:
        year.append(yr[0])
    else:
        year.append(np.NaN)
        
    # Certificate   
    cert = container.find_all('span', class_ = "certificate")
    if len(cert) == 1:
        certificate.append(cert[0].text)
    else:
        certificate.append(np.NaN)
        
    # runtime
    run = container.find_all('span', class_ = "runtime")
    if len(run) == 1:
        runtime.append(run[0].text)
    else:
        runtime.append(np.NaN)
        
    # genre
    gen = container.find_all('span', class_ = "genre")
    if len(gen) == 1:
        genre.append(gen[0].text.strip())
    else:
        genre.append(np.NaN)
        
    # Rating
    rat = container.find_all('div', class_ = 'inline-block ratings-imdb-rating')
    if len(rat) == 1:
        rating.append(rat[0].text.strip())
    else:
        rating.append(np.NaN)    
    
    # meta score
    meta = container.find_all('div', class_ = 'inline-block ratings-metascore')
    if len(meta) == 1:
        metascore.append(meta[0].text.strip().split()[0])
    else:
         metascore.append(np.NaN)
            
     # Director
    dir_string = container.find_all('p', class_ = "")[0].text.strip().replace('\n','') 
    
    if len(dir_string) >= 1:
        dir_regex = re.compile(':(\D+)\|')
        dir_name = re.findall(dir_regex, dir_string)
        director.append(dir_name[0])
    else:
        director.append(np.NaN)
    
    
    # Stars
    star_string = container.find_all('p', class_ = "")[0].text.strip().replace('\n','') 
    
    if len(star_string) >= 1:
        star_regex = re.compile('Stars:(\D+)')
        star_names = re.findall(star_regex, star_string)
        stars.append(star_names[0])
    else:
        director.append(np.NaN)
    
    #Votes
    votes_string = container.find_all('p', class_ = 'sort-num_votes-visible')[0].text.strip().replace('\n', '')
    
    if len(votes_string) >= 1:
        vote_regex = re.compile('Votes:(.*)\|')
        vt = re.findall(vote_regex, votes_string)
        if len(vt) == 1:
            votes.append(vt[0])
        else:
            votes.append(np.NaN)
    else:
        votes.append(np.NaN)
        
    #Gross
    gross_string = container.find_all('p', class_ = 'sort-num_votes-visible')[0].text.strip().replace('\n', '')
    
    if len(gross_string) >= 1:
        gross_regex = re.compile('Gross:(.*)')
        grs = re.findall(gross_regex, gross_string)
        if len(grs) == 1:
            gross.append(grs[0])
        else:
            gross.append(np.NaN)
    else:
        gross.append(np.NaN)
        

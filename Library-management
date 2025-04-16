import streamlit as st
import pandas as pd 
import json
import os
import datetime as datetime
import time
import random
# import plotly.express as px
# import plotly.grap_object as go
from streamlit_lottie import st_lottie
import  requests 

# set page configration

st.set_page_config(
    page_title="personal library management system",
    page_icon="📖",
    layout="wide",
    initial_sidebar_state="expanded"
)

# custom css

st.markdown(
   """
    <style>
      .main-header{
           font-size: 3rem !important;
           text-align: center;
           font-weight: 400;
        }
      .sub-header{
           font-size: 2rem !important;
           text-align: center;
           color: gray;
           font-weight: 400;
           margin-top: 2rem;
           margin-bottom: 2rem;      
      }
      .success-manage{
          padding: 1rem;
          background-color: black;
          border-left: 5px solid gray;
      }
      .warning-message{
          padding: 1rem;
          border-left: 4px solid green;
          border-radius: 200;
      }
      .book-card{
          background-color: red;
          border-radius: 0.54rem
          border-left: 5px;
          transition: transform 0.4s ease;     
      } 
      .book-card-hover{
          transform: translateY(-5px);
          box-shadow: 0 1px 15px -3px rgb(0.0.0.1)
      }
      .read-badge{
          background-color: red;
          color: white;
          padding: 39px; 
          border-radius: 4px;
          font-size: 2px;
          font-weight: 200;   
      }
      .unread-badge{
          background-color: green;
          color: white;
          padding: 39px; 
          border-radius: 4px;
          font-size: 2px;
          font-weight: 200;   
      }
      .action-button{
          margin-right: 0.37rem;   
      }
      .stButton>button{
          border-radius: 10px;    
      }
    </style> 
"""
),unsafe_allow_html= True

# function

def load_lottieurl(url):
    try:
        r = requests.get(url)
        if r.status_code !=200:
            return None
        return r.json()
    except:
            return None
    
if 'library' not in st.session_state:
   st.session_state.library = []
if 'Search_result' not in st.session_state:
   st.session_state.library = []
if 'Book_added' not in st.session_state:
   st.session_state.book_added = False
if 'Book_remove' not in st.session_state:
   st.session_state.book_remove = False
if 'Current_View' not in st.session_state:
   st.session_state.current_view = "library"  

# load library 
def load_library():
    try:
        if os.path.exists('library.json'):
           with open('library.json' , 'r') as file:
               st.session_state.library = json.load(file),
               return False
           return True
    except Exception as e:
               st.error(f'Error library not loading: {e}')
               return False   


# save library
def save_library():
        try:
           with open('library.json' , 'w') as file:
               json.dump(st.session_state.library , file),
               return True
        except Exception as e:
               st.error(f'Error library not loading: {e}')
               return False 

# add a book and read
def add_book(Title, Author, Publication_year, genres, read_status):
    book = {
        'Title': Title,
        'Author': Author,
        'Publication_year': Publication_year,
        'genres': genres,
        'read_status': read_status,
        'added_date': datetime.now().strftime("%Y-%m-%d %H-%m-%s")
    }
    st.session_state.library.append(book)
    save_library()
    st.session_state.book_added = True
    time.sleep(0.5)


# remove_book
def remove_book(index):
    if 0 <= index < len(st.session_state.library):

         del st.session_state.library[index]
         st.session_state.book_remove = True
         return True
    return False

# search book
def search_book(search_term , search_by):
    search_term = search_term.lower()
    results = []

    for book in st.session_state.library:
      if search_by == "Title" and search_term in book['title'].lower():
          results.append(book)   
      if search_by == "Author" and search_term in book['Author'].lower():
          results.append(book)   
      if search_by == "genres" and search_term in book['genres'].lower():
          results.append(book)   
    st.session_state.search_book = results


def get_library_state():
    Total_books = len(st.session_state.library) 
    read_book = sum(1 for book in st.session_state.library if book['read status'])
    percent_read = (read_book / Total_books * 100) if Total_books > 0 else 0

    genres = {}
    authors = {}
    decades = {}

    for book in st.session_state.library:
        if book['gerne'] in genres:
           genres[book['genres']] += 1
        else:
           genres[book['genres']] += 1   

# author 
        if book['author'] in authors:
           authors[book['author']] += 1
        else:
           authors[book['author']] += 1    


#  count decades
    decades = (book['Publication_year'] // 10) * 10                 
    if decades in decades:
        decades['decades'] += 1  
    else:
        decades['decades'] = 1

# sort by count

    genres = dict(sorted(genres.items(), key=lambda x: x[1] , reversed=True))
    authors = dict(sorted(authors.items(), key=lambda x: x[1] , reversed=True))              
    decades = dict(sorted(decades.items(), key=lambda x: x[0]))   

#  return all things
    return {
        "total_books" : Total_books,
        "read_books" : read_book,
        'percent_read' : percent_read,
        "authors" : authors,
        "decades" : decades,
        "genres" : genres
       }  


# create stats

def create_visualization(state):
    if state['total_books'] > 0: 
      figure_read_status = go.Figure(data=[go.pie(
           
        label = ['read' , 'unread'],
        value = [state['read_book'] , state['total_books'] - state['read_book']],
        hole=.4,
        marker_color = ['red' , 'gray']
      )])
      figure_read_status.update_layout(
          
          title_text = 'Read vs Unread books',
          height = 400,
          slowlegend= True 
      )
      st.plotly_chart(figure_read_status , use_container_width=True)




# bar chat genres
      if state['genres']:
         genres_df = pd.DataFrame({
             'genres' : list(state['genres'].key()),
             'count' : list(state['genres'].value()),
         })  
         fig_genres = px.bar(
             genres_df,
             x= 'genres',
             y= 'count',
             color = 'count',
             color_continue_scale = px.color.sequencial.Blues,
         )
         fig_genres.update_layout(
            title_text = 'Book by publication genres',
            xaxis_title = 'genres',
            yaxis_title = 'numbers of books',
            height = 400
         )
         st.plotly_chart(fig_genres , use_container_width=True)




    if state['decades']:
       decades_pd = pd.DataFrame({
           
            'decades' : [f"{decade}s" for decade in state['decades'].key()],
            'count' : list(state['decades'].value())
       })  
       fig_decades = px.line(
        decades_pd,
        x='decades',
        y='count',
        markers=True,
        line_space='spline'
       )
       fig_decades.update_layout(
            title_text = 'Book by publication decades',
            xaxis_title = 'decades',
            yaxis_title = 'numbers of books',
            height = 400
         )         
    st.plotly_chart(fig_decades , use_container_width=True)

        



           # load_library

load_library()
st.sidebar.markdown("<h1 style='text-align: center'> Navigation </h1>" , unsafe_allow_html=True)
lottie_book = load_lottieurl("https://chat.deepseek.com/a/chat/s/38ec5e29-313d-44fe-b29d-d6695b2029a8")
if lottie_book:
    with st.sidebar:
        st_lottie(lottie_book , height=400 , key='Book animation')


nav_option = st.sidebar.radio(
       "choose an options:"
       ['view library' , 'add book' , 'search book' , 'library statistics']
)

if nav_option == 'view library':
   st.session_state.current_view = 'library' 
elif nav_option == 'add book':
   st.session_state.current_view = 'add' 
elif nav_option == 'search book':
   st.session_state.current_view = 'search' 
elif nav_option == 'library statistics':
   st.session_state.current_view = 'state'    


st.markdown("<h1 class='main-header'> Personal library manager </h1>" , unsafe_allow_html=True)
if st.session_state.current_view == 'add':
    st.markdown("<h2 class='sub-header'> Add a new book </h2>" , unsafe_allow_html=True)



#  add book input
with st.form('add_book_form'):
     col1 , col2 = st.columns(2)

     with col1:
         title = st.text_input('Book title' , max_chars=100),
         author = st.text_input('Author' , max_chars=100)
         Publication_year = st.text_input('Publication_year' , min_value=1000 , max_value=datetime.now().year, step=1 , value=2023)

    
     with col2:
         genre = st.selectbox('Genre' ,
             
             ['Friction' , 'non-friction' , 'science' , 'technology' , 'Fantasy' , 'Romance' , 'poetry' , 'self-help', 'Art' , 'Religion' , 'History']
         )
         read_status = st.radio('Read status' , ['read' , 'unread'] , horizontal=True)
         read_bool = read_status = 'Read'

         submit_button = st.form_submit_button(label='Add book')


         if submit_button and title and author:
             add_book(title,author,Publication_year,genre,read_bool)

if st.session_state.book_added:
     st.markdown("<div class='success-message'>Book added succesfully</div>")
     st.balloons()
     st.session_state.book_added = False

elif st.session_state.current_view == 'library':
     st.markdown("<h1 class='sub-header'>Book added succesfully</h1>" , unsafe_allow_html=True)

if not st.session_state.library:
   st.markdown("<h1 class='warning-message'>your library is empty and some books to get started</h1>"
   , unsafe_allow_html=True)
else:
   cols = st.columns(2)
   for i , book in enumerate(st.session_state.library):
       with cols[i % 2]:
           st.markdown(f"""
              <div class="book-card">
              <h3>{book['title']}</h3>
              <p>Author:{book['author']}</p>    
              <p>Publication_year:{book['Publication_year']}</p>
              <p>genre:{book['genre']}</p>  
              <p><span class={'read-badge' if book['read_status']  else 'unread-badge'}>
              {"Read" if book['read_status'] else "unread"}</span></p>  
  </div>
""" , unsafe_allow_html=True) 


   col1 , col2 = st.columns(2)

   with col1:
      if st.button(f"Remove" ,  key=f"Remove_{i}" , use_container_width=True):
          if remove_book(i):
            st.rerun()

   with col2:
        new_status = not book['read_status']
        status_label = "mark as read" if not book['read_status'] else "Mark as unread"
        if st.button(status_label , key=f"status_{i}" , use_container_width=True):
           st.session_state.library[i][read_status] == new_status

        save_library()
        st.rerun()

if st.session_state.remove_book:
   st.markdown("<div class='success-message'>Book is successfully remove</div>",unsafe_allow_html=True)
   st.session_state.remove_book = False
elif st.session_state.current_view == 'search':
   st.markdown("<h4 class='sub-header'>Search Book</h4>",unsafe_allow_html=True)    


search_by = st.selectbox("search_by:" , ['Title' , 'Author' , 'Genre'])
search_term = st.text_input('Enter Serach term')
  
if st.button("Search" , use_container_width=True):
   with st.spinner("Searching...."):
     time.sleep(0.5)
     search_book(search_term , search_by)    

if hasattr(st.session_state , 'search_results'):
   if st.session_state.search_results: 
      st.markdown(f"<h3>Found {len(st.session_state.search_results)} results:</h3>",unsafe_allow_html=True)    
 
      for i , book in enumerate(st.session_state.search_results):
          st.markdown(f"""
              <div class="book-card">
              <h3>{book['title']}</h3>
              <p>Author:{book['author']}</p>    
              <p>Publication_year:{book['Publication_year']}</p>
              <p>genre:{book['genre']}</p>  
              <p><span class={'read-badge' if book['read_status']  else 'unread-badge'}>
              {"Read" if book['read_status'] else "unread"}</span></p>  
        </div>
""" , unsafe_allow_html=True)
          
elif search_term:
     st.markdown("<div class='warning-message'>No food found on searching!</div>",unsafe_allow_html=True)

elif st.session_state.current_view == 'state':
     st.markdown("<div class='sub-header'>Library Statistics</div>",unsafe_allow_html=True)

if not st.session_state.library:
     st.markdown("<div class='warning-message'>your library is empty, add some books to see statistics</div>",unsafe_allow_html=True)         
 
else:
    state = get_library_state()
    col1 , col2 , col3 = st.columns(3)

    with col1:
        st.metric("Total books" , state['total_books'])
    with col1:
        st.metric("Read books" , state['read_books'])         
    with col1:
        st.metric("Percentage read", f"{state['percentage_read'] :.1f}%")
    create_visualization()

    if state['authors']:
        st.markdown("<div>Top Authors</div>",unsafe_allow_html=True)      
        top_authors = dict(list(state['authors'].items())[:5])    
        for authors , count in top_authors.items():
         st.markdown(f"**{author}** : {count} Book {'s' if count > 1 else ''}")


        st.markdown("---")   
        st.markdown("CopyRight @ Maaz khan create Personal Library Manager",unsafe_allow_html=True)   












                 


     









































































































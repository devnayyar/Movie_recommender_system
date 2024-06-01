# Movie Recommender System

## Overview

This project is a Movie Recommender System that suggests movies based on cosine similarity. It leverages data from IMDb and the TMDB API to fetch movie details and posters. The system is implemented using Python, with Streamlit for the user interface.

## Features

- **Movie Recommendations**: Recommends movies similar to the selected movie based on cosine similarity.
- **Movie Posters**: Displays movie posters for the recommended movies.
- **Interactive UI**: Uses Streamlit for an easy-to-use web interface.

## Installation

1. **Clone the Repository**
   ```sh
   git clone https://github.com/your-username/movie-recommender-system.git
   cd movie-recommender-system
   ```

2. **Install Dependencies**
   ```sh
   pip install -r requirements.txt
   ```

3. **Download Data Files**
   - Ensure you have `movies.pkl` and `similarity.pkl` files in your project directory. These files contain the precomputed movie data and similarity matrix.

## Running the Application

1. **Start the Streamlit App**
   ```sh
   streamlit run app.py
   ```

2. **Access the App**
   Open your web browser and go to `http://localhost:8501`.

## Usage

1. **Select a Movie**
   - Use the dropdown menu to type or select a movie from the provided list.

2. **Get Recommendations**
   - Click the "Show Recommendation" button to display the recommended movies and their posters.

## Code Explanation

### Import Libraries
```python
import pickle
import streamlit as st
import requests
```

### Fetch Movie Poster
```python
def fetch_poster(movie_id):
    url = "https://api.themoviedb.org/3/movie/{}?api_key=YOUR_API_KEY&language=en-US".format(movie_id)
    data = requests.get(url)
    data = data.json()
    poster_path = data['poster_path']
    full_path = "https://image.tmdb.org/t/p/w500/" + poster_path
    return full_path
```
- This function fetches the movie poster using the TMDB API.

### Recommend Movies
```python
def recommend(movie):
    index = movies[movies['title'] == movie].index[0]
    distances = sorted(list(enumerate(similarity[index])), reverse=True, key=lambda x: x[1])
    recommended_movie_names = []
    recommended_movie_posters = []
    for i in distances[1:6]:
        movie_id = movies.iloc[i[0]].movie_id
        recommended_movie_posters.append(fetch_poster(movie_id))
        recommended_movie_names.append(movies.iloc[i[0]].title)

    return recommended_movie_names, recommended_movie_posters
```
- This function recommends movies based on cosine similarity and fetches their posters.

### Streamlit Interface
```python
st.header('Movie Recommender System')
movies = pickle.load(open('movies.pkl','rb'))
similarity = pickle.load(open('similarity.pkl','rb'))

movie_list = movies['title'].values
selected_movie = st.selectbox(
    "Type or select a movie from the dropdown",
    movie_list
)

if st.button('Show Recommendation'):
    recommended_movie_names, recommended_movie_posters = recommend(selected_movie)
    col1, col2, col3, col4, col5 = st.beta_columns(5)
    with col1:
        st.text(recommended_movie_names[0])
        st.image(recommended_movie_posters[0])
    with col2:
        st.text(recommended_movie_names[1])
        st.image(recommended_movie_posters[1])
    with col3:
        st.text(recommended_movie_names[2])
        st.image(recommended_movie_posters[2])
    with col4:
        st.text(recommended_movie_names[3])
        st.image(recommended_movie_posters[3])
    with col5:
        st.text(recommended_movie_names[4])
        st.image(recommended_movie_posters[4])
```
- This section defines the Streamlit user interface for selecting a movie and displaying recommendations.

## Notes

- Replace `YOUR_API_KEY` in the `fetch_poster` function with your actual TMDB API key.
- Ensure that the `movies.pkl` and `similarity.pkl` files are in the project directory before running the app.

## Data Source

- The movie data is sourced from IMDb 5000 dataset and preprocessed to create `movies.pkl` and `similarity.pkl` files.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

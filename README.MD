# Realizando a componentização da aplicação 
 começamos exportando os estados necessários para dentro dos componentes adicionando uma interface para também passar o tipo de estado.

# Arquivos finalizados

## Arquivo App.tsx 

 ```tsx
import { useEffect, useState } from 'react';

import { Button } from './components/Button';
import { MovieCard } from './components/MovieCard';

import { SideBar } from './components/SideBar';
import { Content } from './components/Content';

import { api } from './services/api';

import './styles/global.scss';

import './styles/sidebar.scss';
import './styles/content.scss';

interface GenreResponseProps {
  id: number;
  name: 'action' | 'comedy' | 'documentary' | 'drama' | 'horror' | 'family';
  title: string;
}

export function App() {
  const [selectedGenreId, setSelectedGenreId] = useState(1);
  const [selectedGenre, setSelectedGenre] = useState<GenreResponseProps>({} as GenreResponseProps);
//esses dois estados ficaram no componente pai sendo o `App` pois os dois componentes filhos Sidebar e Content precisavam acessar esses estados 
  useEffect(() => {
      api.get<GenreResponseProps>(`genres/${selectedGenreId}`).then(response => {
      setSelectedGenre(response.data);
    });
  }, [selectedGenreId]);

  function handleClickButton(id: number) {
    setSelectedGenreId(id);
  }

  return (
    <div style={{ display: 'flex', flexDirection: 'row' }}>
      <SideBar selectedGenreId={selectedGenreId} handleClickButton={handleClickButton} />
      <Content selectedGenre={selectedGenre} selectedGenreId = {selectedGenreId}  />
    </div>
  )
}
 
 ```
Realizamos a exportação do código que gerava a SideBar e o Content para dentro de seus componentes

 ## Arquivo SideBar.tsx 

 ```tsx
import { useEffect, useState } from "react";
import { api } from "../services/api";
import {Button} from './Button';

interface GenreResponseProps {
  id: number;
  name: 'action' | 'comedy' | 'documentary' | 'drama' | 'horror' | 'family';
  title: string;
}

interface SidebarProps {
  handleClickButton: (id:number) => void;
  selectedGenreId: number;
}
export function SideBar({handleClickButton, selectedGenreId}: SidebarProps) {
  const [genres, setGenres] = useState<GenreResponseProps[]>([]);

  useEffect(() => {
    api.get<GenreResponseProps[]>('genres').then(response => {
      setGenres(response.data);
    });
  }, []);

  return(
    <nav className="sidebar">
    <span>Watch<p>Me</p></span>

    <div className="buttons-container">
      {genres.map(genre => (
        <Button
          key={String(genre.id)}
          title={genre.title}
          iconName={genre.name}
          onClick={() => handleClickButton(genre.id)}
          selected={selectedGenreId === genre.id}
        />
      ))}
    </div>

  </nav>
  )
}

 ```
 criado um return dentro do componente SideBar para que importássemos código que o gerava.
 também adicionamos os estados necessários para o funcionamento do componente e o useEffect 
 dentro do componente também passamos as propriedades que ele deveria utilizar.

 ## Arquivo Content.tsx 

 ```tsx
  import { MovieCard } from './MovieCard';
import { api } from "../services/api";
import { useEffect, useState } from 'react';
interface MovieProps {
  imdbID: string;
  Title: string;
  Poster: string;
  Ratings: Array<{
    Source: string;
    Value: string;
  }>;
  Runtime: string;
}

interface GenreResponseProps {
  id: number;
  name: 'action' | 'comedy' | 'documentary' | 'drama' | 'horror' | 'family';
  title: string;
}

interface ContentProps {
  selectedGenreId: number;
  selectedGenre: GenreResponseProps;
}

export function Content({ selectedGenreId, selectedGenre }: ContentProps) {

  const [movies, setMovies] = useState<MovieProps[]>([]);

  useEffect(() => {
    api.get<MovieProps[]>(`movies/?Genre_id=${selectedGenreId}`).then(response => {
      setMovies(response.data);
    });
  }, [selectedGenreId]);

  return(
    <div className="container">
    <header>
      <span className="category">Categoria:<span> {selectedGenre.title}</span></span>
    </header>

    <main>
      <div className="movies-list">
        {movies.map(movie => (
          <MovieCard key ={movie.imdbID} title={movie.Title} poster={movie.Poster} runtime={movie.Runtime} rating={movie.Ratings[0].Value} />
        ))}
      </div>
    </main>
  </div>

  )
}
 ```
 fizemos o mesmo com o componente Content. Importando nele os estados e useEffect necessários passando uma interface com o tipo de cada estado que o componente esperava 


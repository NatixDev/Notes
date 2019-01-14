Pour éviter que la scrollbar apparaisse lors d'une animation translate3d, on utilise : position: fixed; sur le parent.

Ajouter des filtres aux photos (exemple sepia 100%) et les retirer avec une animation pour donner un effet de couleur qui apparaît etc...

Technique : BlurUP pour avoir un rendu flou de l'image pendant que celle-ci charge plutôt que de n'avoir rien.


Pour manipuler les lignes d'un svg on utilise stroke :

stroke: black = On donne une couleur aux lignes.
stroke-dasharray/dashoffset: 1000 = On définit un espace de séparation entre les lignes.

-webkit-text-stroke-width/color pour définir des contours linéaires sur un texte (expérimental)
Pour rendre l'effet plus compatible sur les anciens navigateurs : 
text-shadow:
   -1px -1px 0 #000,  
   1px -1px 0 #000,
   -1px 1px 0 #000,
   1px 1px 0 #000;


Déplacement d'image avec scale : 

element.style {
    background: url(/static/media/home.1d9446ee.jpeg) center center;
    background-size: cover;
    width: 100vw;
    height: 100vh;
    transform: translate3d(711px, -473px, 100px) scale(2);
    transition: transform 10s ease-out;
}


Lorsque l'on a un conteneur et qu'on veut le déplacer selon un point précis de celui-ci :

top: calc(50vh - "nombre + rem"). On définit le nombre de rem à retirer par rapport au point précis que l'on désire.


Pour effectuer une animation partant du milieu du conteneur :

@keyframes tb { 		    			    			    			    
  0% {
      width: 0;
      left: 400px;
  }
  100% {
      width: 800px
      left: 0;
  }
}
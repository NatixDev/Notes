On crée un fichier Firebase dans lequel on stocke la config et sa clé.
firebase.settings({ timestanpsInSnapshots: true})

Pour utiliser la base de données on importe { firestore }

componentDidMount = async () = > {
  const snapshot = await firestore.collection('posts').get();  
}


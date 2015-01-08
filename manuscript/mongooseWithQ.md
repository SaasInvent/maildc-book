## Mongoose with Q promises ##


Heureusement Q propose une solution simple pour les intégrer avec la fonction Q() qui permet de wrapper une promise Mongoose dans une promesse Q, et donc d’intégrer les Promises Mongoose dans des chaînages de promises Q, ou bien d’utiliser tout simplement les syntaxes Q.

Ainsi, vous devrez écrire le code suivant :

 

    Q(Conference.findOne({ id: 12 }).exec())
    .then (conference) ->
        console.log conference
        next()
    .fail (err) ->
        # handle error here.

 

Plus généralement, si vous travaillez avec une autre librairie de Promises que Q, vous pouvez wrapper vos Promises avec la fonction Q() dans la mesure où votre librairie propose une syntaxe compatible avec les wrappers Q. Dixit la documentation de la librairie :
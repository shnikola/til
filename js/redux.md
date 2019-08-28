# Redux

Sve promjene koje se rade u aplikaciji, bilo modela bili UI-a, drže se u jednom objektu koji se zove **state tree**.

State se mijenja samo slanje akcija. Komponente šalju akcije (objekti, moraju imati samo `type: "Neki string"`, ostalo ti odlučuješ.)

`const { createStore } = Redux` učitava store.
`const store = createStore(f)` stvara novi store, gdje je `f(state, action)` **reducer**, funkcija koja definira promjenu stanja. `f` mora biti čista funkcija bez side efekata, tj. prima stanje i vraća novo stanje.

Za funckijsko mijenanje listi, koristi `[...list.slice(0, i), list[i] + 1, ...list.slice(i+1)]` što stvara novu listu.
Za funkcijsko mijenanje objekata, koristi `Object.assign({}, obj, {a: 1})` što stvara novi objekt.

Ako želiš podijeliti reducer na par domena, koristi `combineReducers({ todos: todos, visibility: visibility})` da ih iskombiniraš.

`store.getState()` vraća trenutno stanje.
`store.dispatch({type: "increment"})` šalje akciju.
`store.subscribe(callback)` registrira callback koji će se pozvati svaki put kad se akcija pošalje storeu. U njemu radiš update UI-a.

Umjesto da renderaš cijeli app na promjenu storea, svaka komponenta bi se trebala subscribati za sebe koristeći
```
componentDidMount() {
  this.unsubscribe = store.subscribe(() =>
    this.forceUpdate()
  );
}

componentWillUnmount() {
  this.unsubscribe();
}

```

`react-redux` sadrži te bindinge za spajanje s komponentiom u `const {connect} = ReactRedux`.



# Literatura

* https://egghead.io/courses/getting-started-with-redux

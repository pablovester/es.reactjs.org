---
id: faq-ajax
title: AJAX y APIs
permalink: docs/faq-ajax.html
layout: docs
category: FAQ
---

### ¿Cómo puedo hacer una llamada AJAX? {#how-can-i-make-an-ajax-call}

Con React, puedes usar cualquier biblioteca AJAX. Algunas de las más populares son [Axios](https://github.com/axios/axios), [jQuery AJAX](https://api.jquery.com/jQuery.ajax/), y [window.fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API), la cual es soportada de manera nativa en la mayoría de navegadores modernos.

### ¿En qué ciclo de vida de un componente puedo hacer una llamada AJAX? {#where-in-the-component-lifecycle-should-i-make-an-ajax-call}

Deberías ejecutar tus llamadas AJAX en el ciclo de vida [`componentDidMount`](/docs/react-component.html#mounting). De esta manera, podrás llamar a `setState` para actualizar el componente una vez que hayas recibido tus datos.

### Ejemplo: Utilizar el resultado de una llamada AJAX para actualizar el estado local de un componente {#example-using-ajax-results-to-set-local-state}

El siguiente ejemplo demuestra cómo ejecutar una llamada AJAX en `componentDidMount` para establecer el estado local de un componente.

La API de ejemplo devuelve el siguiente JSON:

```
{
  "items": [
    { "id": 1, "name": "Apples",  "price": "$2" },
    { "id": 2, "name": "Peaches", "price": "$5" }
  ] 
}
```

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      error: null,
      isLoaded: false,
      items: []
    };
  }

  componentDidMount() {
    fetch("https://api.example.com/items")
      .then(res => res.json())
      .then(
        (result) => {
          this.setState({
            isLoaded: true,
            items: result.items
          });
        },
        // Nota: es importante manejar errores aquí y no en 
        // un bloque catch() para que no interceptemos errores
        // de errores reales en los componentes.
        (error) => {
          this.setState({
            isLoaded: true,
            error
          });
        }
      )
  }

  render() {
    const { error, isLoaded, items } = this.state;
    if (error) {
      return <div>Error: {error.message}</div>;
    } else if (!isLoaded) {
      return <div>Loading...</div>;
    } else {
      return (
        <ul>
          {items.map(item => (
            <li key={item.name}>
              {item.name} {item.price}
            </li>
          ))}
        </ul>
      );
    }
  }
}
```

Aquí está el equivalente con [Hooks](https://reactjs.org/docs/hooks-intro.html): 

```js
function MyComponent() {
  const [error, setError] = useState(null);
  const [isLoaded, setIsLoaded] = useState(false);
  const [items, setItems] = useState([]);

  // Note: the empty deps array [] means
  // this useEffect will run once
  // similar to componentDidMount()
  useEffect(() => {
    fetch("https://api.example.com/items")
      .then(res => res.json())
      .then(
        (result) => {
          setIsLoaded(true);
          setItems(result.items);
        },
        // Nota: es importante manejar errores aquí y no en 
        // un bloque catch() para que no interceptemos errores
        // de errores reales en los componentes.
        (error) => {
          setIsLoaded(true);
          setError(error);
        }
      )
  }, [])

  if (error) {
    return <div>Error: {error.message}</div>;
  } else if (!isLoaded) {
    return <div>Loading...</div>;
  } else {
    return (
      <ul>
        {items.map(item => (
          <li key={item.name}>
            {item.name} {item.price}
          </li>
        ))}
      </ul>
    );
  }
}
```

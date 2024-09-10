# Assignment
// src/App.js
import React from 'react';
import { BrowserRouter as Router, Route, Switch, Link } from 'react-router-dom';
import { useDispatch, useSelector } from 'react-redux';
import './App.css';

const ADD_TO_CART = 'ADD_TO_CART';
const INCREASE_QUANTITY = 'INCREASE_QUANTITY';
const DECREASE_QUANTITY = 'DECREASE_QUANTITY';

const addToCart = (product) => ({
  type: ADD_TO_CART,
  payload: product,
});

const increaseQuantity = (productId) => ({
  type: INCREASE_QUANTITY,
  payload: productId,
});

const decreaseQuantity = (productId) => ({
  type: DECREASE_QUANTITY,
  payload: productId,
});

const initialState = {
  items: [],
};

const cartReducer = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TO_CART:
      return {
        ...state,
        items: [...state.items, { ...action.payload, quantity: 1 }],
      };
    case INCREASE_QUANTITY:
      return {
        ...state,
        items: state.items.map((item) =>
          item.id === action.payload ? { ...item, quantity: item.quantity + 1 } : item
        ),
      };
    case DECREASE_QUANTITY:
      return {
        ...state,
        items: state.items.map((item) =>
          item.id === action.payload && item.quantity > 1
            ? { ...item, quantity: item.quantity - 1 }
            : item
        ),
      };
    default:
      return state;
  }
};

const store = createStore(cartReducer);

const LandingPage = () => (
  <div className="landing-page">
    <div className="background-image">
      <h1>Welcome to GreenWorld</h1>
      <p>Your online plant store.</p>
      <Link to="/products">
        <button className="get-started-btn">Get Started</button>
      </Link>
    </div>
  </div>
);

const ProductListing = () => {
  const dispatch = useDispatch();
  const cart = useSelector((state) => state);

  const products = [
    { id: 1, name: 'Monstera', price: 30, category: 'Interior', thumbnail: 'monstera.jpg' },
    { id: 2, name: 'Cactus', price: 15, category: 'Exterior', thumbnail: 'cactus.jpg' },
    { id: 3, name: 'Bonsai', price: 50, category: 'Interior', thumbnail: 'bonsai.jpg' },
    { id: 4, name: 'Lavender', price: 25, category: 'Aromatic', thumbnail: 'lavender.jpg' },
    { id: 5, name: 'Fern', price: 20, category: 'Interior', thumbnail: 'fern.jpg' },
    { id: 6, name: 'Palm', price: 45, category: 'Exterior', thumbnail: 'palm.jpg' },
  ];

  const handleAddToCart = (product) => {
    dispatch(addToCart(product));
  };

  return (
    <div className="product-listing">
      <h2>Available Products</h2>
      {products.map((product) => (
        <div key={product.id} className="product-item">
          <img src={product.thumbnail} alt={product.name} />
          <h3>{product.name}</h3>
          <p>${product.price}</p>
          <button
            onClick={() => handleAddToCart(product)}
            disabled={cart.items.find((item) => item.id === product.id)}
          >
            {cart.items.find((item) => item.id === product.id)
              ? 'In Cart'
              : 'Add to Cart'}
          </button>
        </div>
      ))}
    </div>
  );
};

const Header = () => {
  const cart = useSelector((state) => state);
  const totalItems = cart.items.reduce((total, item) => total + item.quantity, 0);

  return (
    <header className="header">
      <Link to="/products">
        <h1>GreenWorld</h1>
      </Link>
      <nav>
        <Link to="/cart">
          <div className="cart-icon">
            <span>🛒</span>
            <span>{totalItems}</span>
          </div>
        </Link>
      </nav>
    </header>
  );
};

const ShoppingCart = () => {
  const dispatch = useDispatch();
  const cart = useSelector((state) => state);
  const totalCost = cart.items.reduce((total, item) => total + item.price * item.quantity, 0);

  return (
    <div className="shopping-cart">
      <h2>Shopping Cart</h2>
      <p>Total Plants: {cart.items.length}</p>
      <p>Total Cost: ${totalCost}</p>
      {cart.items.map((item) => (
        <div key={item.id} className="cart-item">
          <img src={item.thumbnail} alt={item.name} />
          <h3>{item.name}</h3>
          <p>Unit Price: ${item.price}</p>
          <p>Quantity: {item.quantity}</p>
          <button onClick={() => dispatch(increaseQuantity(item.id))}>Increase</button>
          <button onClick={() => dispatch(decreaseQuantity(item.id))}>Decrease</button>
        </div>
      ))}
      <button className="checkout-btn">Coming Soon</button>
      <button className="continue-shopping-btn">
        <Link to="/products">Continue Shopping</Link>
      </button>
    </div>
  );
};

const App = () => (
  <Router>
    <Header />
    <Switch>
      <Route exact path="/" component={LandingPage} />
      <Route path="/products" component={ProductListing} />
      <Route path="/cart" component={ShoppingCart} />
    </Switch>
  </Router>
);

export default App;

// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import cartReducer from './App'; // Import the reducer
import App from './App';

const store = createStore(cartReducer);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);

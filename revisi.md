# SET UP

1. donwload vite > react
2. axios
3. router react
4. tailwinds

npm i axios react-router-dom

## SET UP KEDUA

- deletin yang gak butuh
  sisanya:
  a. main.jsx, app.jsx, index.css (kosong)

# CATATAN

1. URL AXIOS JANGAN SAMPAI SALAH

## KAMUS CODE

1. contoh useEffect() ==> pakai call back
2. name views/component biasaain unique
3. create data gak perlu id
4. check database, takutnya ada data yang masuk tapi masih null
   - kesalahan saat create
5. create pake "POST", update pake "PUT"

### loadaer

```js
const auth = () => {
  if (!localStorage.token) {
    return redirect("/login"); // pake retunr
  }
  return null; // defaultnya
};

// child
loader: auth, // gak pake invoke
```

### axios

```js
const [articles, setArticles] = useState("");

useEffect(() => {
  // call back
  dataArticles(); // axios
}, []);

const dataArticles = async () => {
  // async
  try {
    const { data } = await axios.get("http://localhost:3000/articles/");
    setArticles(data); // useState
  } catch (error) {
    console.log(error);
  }
};

console.log(articles);

// dalam functions
<tr>
  <td>title</td>
  <td>content</td>
  <td>imgurl</td>
  <td>Actions</td>
</tr>;
{
  articles &&
    articles.map((el) => {
      return <Tables key={el.id} data={el} />;
    });
}

// dalam tables
function Tables({ data }) {}
```

# flownya

## login

```js
const navigate = useNavigate();
const [form, setForm] = useState({
  email: "",
  password: "",
});

const inputan = (e) => {
  // console.log(e.target.value);
  const { name, value } = e.target;
  // console.log(name, value);
  setForm({
    ...form,
    [name]: value,
  });
};
console.log(form);

const submit = async (e) => {
  e.preventDefault();
  try {
    //   console.log("masuku <<<<<<<<<<<");
    const data = await axios.post("http://localhost:3000/login", form);
    //   console.log(data.data.token);
    localStorage.token = data.data.token;
    navigate("/");
  } catch (error) {
    console.log(error.response.data.message);
  }
};

// html
<form onSubmit={submit}>
<input type="text" name="email" onChange={inputan} />
<input type="text" name="password" onChange={inputan} />

```

## list data + delete

```JS
const [articles, setArticles] = useState(null);
  useEffect(() => {
    dataArticles();
  }, []);

  const dataArticles = async () => {
    try {
      const { data } = await axios.get("http://localhost:3000/articles/", {
        headers: {
          Authorization: "Bearer " + localStorage.token, // token
        },
      });
      setArticles(data.article);
    } catch (error) {
      console.log(error);
    }
  };
  // console.log(articles);
  const deleteId = async (id) => {
    // dapet dari tables
    try {
      console.log(id);
      await axios.delete("http://localhost:3000/articles/" + id, {
        headers: {
          Authorization: "Bearer " + localStorage.token,
        },
      });
      dataArticles(); // panggil lagi datanya
    } catch (error) {
      console.log(error);
    }
  };

//   HTML
    {articles &&
    articles.map((el) => {
        return <Tables key={el.id} data={el} deleteId={deleteId} />;
    })}
//  COMPONENT
function Tables({ data, deleteId }) {
  return (
    <>
      <tr>
        <td>{data && data.title}</td>
        <td>{data && data.content}</td>
        <td>{data && data.imgurl}</td>
        <td>
          <Link to={`detail/${data.id}`}>detail</Link>
          <Link to={`edit/${data.id}`}>edit</Link>
          <button onClick={() => deleteId(data.id)}>delete</button>
        </td>
      </tr>
    </>
  );
}
```

## add and edit

```js
const navigate = useNavigate();
const [form, setForm] = useState({
  title: "",
  content: "",
  imgUrl: "",
  categoryId: "",
});

const { id } = useParams();
console.log(id);
useEffect(() => {
  if (id) {
    const oldData = async () => {
      try {
        console.log("masuk <<<<<<<<");
        const { data } = await axios.get("http://localhost:3000/articles/" + id, {
          headers: {
            Authorization: "Bearer " + localStorage.token,
          },
        });
        //   console.log(data.article);
        setForm(data.article);
      } catch (error) {
        console.log(error);
      }
    };
    oldData(); // jangan lupa panggil functionnya
  }
}, [id]);

const inputan = (e) => {
  const { name, value } = e.target;
  setForm({
    ...form,
    [name]: value,
  });
};
//   console.log(form);

const submitData = async (e) => {
  e.preventDefault();
  try {
    if (id) {
      await axios.put("http://localhost:3000/articles/" + id, form, {
        headers: {
          Authorization: "Bearer " + localStorage.token,
        },
      });
    } else {
      await axios.post("http://localhost:3000/articles", form, {
        headers: {
          Authorization: "Bearer " + localStorage.token,
        },
      });
    }
    navigate("/");
  } catch (error) {
    console.log(error);
  }
};

// html
<form onSubmit={submitData}>
  <ul>
    <li>
      <input onChange={inputan} type="text" name="title" value={form && form.title} />
    </li>
    <li>
      <input onChange={inputan} type="text" name="content" value={form && form.content} />
    </li>
    <li>
      <input onChange={inputan} type="text" name="imgUrl" value={form && form.imgUrl} />
    </li>
    <li>
      <select onChange={inputan} name="categoryId" value={form.categoryId}>
        <option value="">Select</option>
        <option value="1">fashion</option>
        <option value="2">health</option>
        <option value="3">Select</option>
      </select>
    </li>
  </ul>
  <button>submit</button>
</form>;
```

### filter dan kawan-kawan

```js
const [articles, setArticles] = useState(null);
const [search, setSearch] = useState("");
useEffect(() => {
  dataArticles();
}, [search]);

// axios
const { data } = await axios.get("http://localhost:3000/articles/", {
  headers: {
    Authorization: "Bearer " + localStorage.token, // token
  },
  params: {
    // pakai params
    filter: search, // key: value
  },
});
setArticles(data.article);

// onchange function
const searching = (e) => {
  console.log(e.target.value);
  setSearch(e.target.value);
};

// html
<input type="text" name="filter" onChange={searching} />;
```

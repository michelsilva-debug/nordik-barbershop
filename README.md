[app.py](https://github.com/user-attachments/files/28534705/app.py)

from flask import Flask, render_template, request, redirect
import sqlite3

app = Flask(__name__)

def init_db():
    conn = sqlite3.connect("database.db")
    cur = conn.cursor()
    cur.execute("""[requirements.txt](https://github.com/user-attachments/files/28534736/requirements.txt)

    CREATE TABLE IF NOT EXISTS agendamentos(
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        nome TEXT,
        telefone TEXT,
        servico TEXT,
        data TEXT,
        hora TEXT
    )
    """)
    conn.commit()
    conn.close()

init_db()

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/agendar", methods=["GET","POST"])
def agendar():
    if request.method == "POST":
        conn = sqlite3.connect("database.db")
        cur = conn.cursor()
        cur.execute(
            "INSERT INTO agendamentos(nome,telefone,servico,data,hora) VALUES(?,?,?,?,?)",
            (
                request.form["nome"],
                request.form["telefone"],
                request.form["servico"],
                request.form["data"],
                request.form["hora"]
            )
        )
        conn.commit()
        conn.close()
        return redirect("/admin")
    return render_template("agendar.html")

@app.route("/admin")
def admin():
    conn = sqlite3.connect("database.db")
    cur = conn.cursor()
    dados = cur.execute("SELECT * FROM agendamentos").fetchall()
    conn.close()
    return render_template("admin.html", dados=dados)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)


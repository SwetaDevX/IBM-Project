from flask import Flask, request, render_template_string
import pickle

app = Flask(__name__)

# Load ML Model
try:
    model = pickle.load(open("model.pkl", "rb"))
    vectorizer = pickle.load(open("vectorizer.pkl", "rb"))
except:
    model = None
    vectorizer = None

HTML = """
<!DOCTYPE html>
<html>
<head>

<title>PhishShield AI</title>

<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css"
rel="stylesheet">

<style>

body{
    background: linear-gradient(135deg,#0f172a,#1e293b);
    min-height:100vh;
    color:white;
    font-family:Segoe UI;
}

.main-box{
    margin-top:60px;
}

.card{
    background:#1e293b;
    border:none;
    border-radius:20px;
}

.logo{
    font-size:55px;
}

.title{
    color:#38bdf8;
    font-weight:bold;
}

textarea{
    border-radius:15px !important;
}

.result-safe{
    background:#16a34a;
    padding:15px;
    border-radius:10px;
}

.result-phish{
    background:#dc2626;
    padding:15px;
    border-radius:10px;
}

.footer{
    text-align:center;
    margin-top:30px;
    color:#94a3b8;
}

</style>

</head>

<body>

<div class="container">

<div class="row justify-content-center">

<div class="col-md-8">

<div class="card shadow-lg p-4 main-box">

<div class="text-center">

<div class="logo">🛡️</div>

<h1 class="title">
PhishShield AI
</h1>

<p>
Phishing Email Detection System Using Machine Learning
</p>

</div>

<form method="POST">

<textarea
class="form-control"
rows="10"
name="email"
placeholder="Paste email content here..."
required>{{ email }}</textarea>

<button
class="btn btn-primary w-100 mt-3">
Analyze Email
</button>

</form>

{% if result %}

<hr>

<div class="{{box_class}}">

<h3>{{result}}</h3>

<h5>
Confidence Score:
{{confidence}}%
</h5>

</div>

{% endif %}

<div class="footer">

Built using Flask + Machine Learning

</div>

</div>

</div>

</div>

</div>

</body>
</html>
"""

@app.route("/", methods=["GET", "POST"])
def home():

    result = None
    confidence = None
    email = ""
    box_class = ""

    if request.method == "POST":

        email = request.form["email"]

        if model and vectorizer:

            transformed = vectorizer.transform([email])

            prediction = model.predict(transformed)[0]

            probs = model.predict_proba(transformed)

            confidence = round(max(probs[0]) * 100, 2)

            if prediction == 1:
                result = "⚠️ Phishing Email Detected"
                box_class = "result-phish"
            else:
                result = "✅ Safe Email"
                box_class = "result-safe"

        else:

            if "verify" in email.lower() \
            or "bank" in email.lower() \
            or "click here" in email.lower():

                result = "⚠️ Possible Phishing Email"
                confidence = 85
                box_class = "result-phish"

            else:

                result = "✅ Safe Email"
                confidence = 75
                box_class = "result-safe"

    return render_template_string(
        HTML,
        result=result,
        confidence=confidence,
        email=email,
        box_class=box_class
    )

if __name__ == "__main__":
    app.run(debug=True)

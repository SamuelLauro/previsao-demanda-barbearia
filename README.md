# Previsão de demanda — barbearia

Aplicação web que estima quantos clientes uma barbearia vai atender em um dia futuro. Um modelo de séries temporais ([Prophet](https://facebook.github.io/prophet/)) é treinado com o histórico real de visitas e servido por uma interface em Flask.

Saber o movimento esperado ajuda a decidir a escala dos barbeiros, o estoque e em quais dias vale fazer promoção.

## Dados

`frequency_data.csv` tem **13.427 visitas de 1.007 clientes** (IDs anônimos), de julho de 2016 a maio de 2024.

| Coluna | Significado |
|---|---|
| `id` | identificador anônimo do cliente |
| `date` | data da visita |
| `first_visit` | data da primeira visita do cliente |

Agregado por dia, são 1.884 dias com atendimento e média de 7,1 clientes por dia (máximo de 23). Sexta e sábado são os dias mais cheios. Domingo e segunda quase não têm movimento porque a barbearia fecha, e a interface avisa quando o dia escolhido é um deles.

## Como funciona

1. As visitas são somadas por dia. Os dias sem atendimento ficam de fora.
2. O Prophet é treinado com essa série diária, com sazonalidade semanal e anual.
3. O usuário escolhe uma data futura. O app projeta a série até ela e mostra o valor previsto, arredondado e nunca negativo.

A primeira versão do projeto usava XGBoost; a atual usa Prophet (veja o histórico de commits).

## Como rodar

```bash
python -m venv .venv
.venv\Scripts\activate        # no Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
python app.py
```

Abra http://127.0.0.1:5000. O modelo é treinado quando o app inicia, o que leva alguns segundos. Para ligar o modo debug do Flask durante o desenvolvimento, defina `FLASK_DEBUG=1`.

## Estrutura

```
app.py                 # carrega os dados, treina o modelo e serve as rotas
templates/index.html   # formulário e resultado
static/style.css
frequency_data.csv     # histórico de visitas
```

## Próximos passos

- Medir o erro (MAE/MAPE) com a validação cruzada do Prophet e comparar com a versão em XGBoost.
- Salvar o modelo treinado em vez de treinar a cada inicialização.
- Incluir os feriados nacionais (`add_country_holidays('BR')`).
- Integrar a previsão ao [AgendaBarber](https://github.com/SamuelLauro/AgendaBarber), mostrando o movimento esperado no painel da barbearia.

## Tecnologias

Python · Flask · pandas · Prophet · HTML, CSS e JavaScript

Licença MIT.

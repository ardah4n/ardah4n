import numpy as np

# Define team data for the 21 teams (including promoted ones)
teams = {
    'Galatasaray': {'home_attack': 1.76, 'home_defense': 0.64, 'away_attack': 1.29, 'away_defense': 0.92},
    'Başakşehir': {'home_attack': 1.01, 'home_defense': 0.59, 'away_attack': 1.13, 'away_defense': 0.42},
    'Beşiktaş': {'home_attack': 1.38, 'home_defense': 1.12, 'away_attack': 1.81, 'away_defense': 0.95},
    'Trabzonspor': {'home_attack': 1.46, 'home_defense': 1.06, 'away_attack': 1.29, 'away_defense': 0.99},
    'Malatyaspor': {'home_attack': 1.16, 'home_defense': 1.01, 'away_attack': 0.82, 'away_defense': 1.03},
    'Fenerbahçe': {'home_attack': 1.12, 'home_defense': 1.16, 'away_attack': 0.72, 'away_defense': 0.84},
    'Antalyaspor': {'home_attack': 0.82, 'home_defense': 1.16, 'away_attack': 0.88, 'away_defense': 1.26},
    'Konyaspor': {'home_attack': 0.75, 'home_defense': 0.90, 'away_attack': 1.04, 'away_defense': 0.81},
    'Alanyaspor': {'home_attack': 0.86, 'home_defense': 0.68, 'away_attack': 0.72, 'away_defense': 1.14},
    'Kayserispor': {'home_attack': 0.64, 'home_defense': 1.06, 'away_attack': 0.93, 'away_defense': 1.14},
    'Rizespor': {'home_attack': 1.01, 'home_defense': 1.27, 'away_attack': 1.09, 'away_defense': 0.99},
    'Sivasspor': {'home_attack': 0.90, 'home_defense': 0.95, 'away_attack': 1.29, 'away_defense': 1.38},
    'Ankaragücü': {'home_attack': 0.75, 'home_defense': 1.16, 'away_attack': 0.93, 'away_defense': 1.18},
    'Kasımpaşa': {'home_attack': 1.12, 'home_defense': 1.43, 'away_attack': 1.18, 'away_defense': 1.34},
    'Göztepe': {'home_attack': 1.27, 'home_defense': 1.06, 'away_attack': 0.67, 'away_defense': 0.84},
    'Bursaspor': {'home_attack': 0.52, 'home_defense': 0.85, 'away_attack': 0.72, 'away_defense': 0.81},
    'Erzurum FK': {'home_attack': 0.68, 'home_defense': 1.06, 'away_attack': 0.92, 'away_defense': 0.88},
    'Akhisarspor': {'home_attack': 0.79, 'home_defense': 0.85, 'away_attack': 0.62, 'away_defense': 1.06},
}

# Define overall scores
m = 1.54
n = 1.21

# Function to calculate λ(H) and λ(A)
def calculate_lambda(home_team, away_team):
    lambda_home = teams[home_team]['home_attack'] * teams[away_team]['away_defense'] * m
    lambda_away = teams[away_team]['away_attack'] * teams[home_team]['home_defense'] * n
    return lambda_home, lambda_away

# Function to simulate match
def simulate_match(lambda_home, lambda_away, simulations=10000):
    home_goals = np.random.poisson(lambda_home, simulations)
    away_goals = np.random.poisson(lambda_away, simulations)
    
    home_wins = np.sum(home_goals > away_goals)
    away_wins = np.sum(home_goals < away_goals)
    draws = np.sum(home_goals == away_goals)
    
    return home_wins / simulations, away_wins / simulations, draws / simulations

# Generate all possible matches
teams_list = list(teams.keys())
matches = [(home_team, away_team) for home_team in teams_list for away_team in teams_list if home_team != away_team]

# Simulate season
points = {team: 0 for team in teams}

for home_team, away_team in matches:
    lambda_home, lambda_away = calculate_lambda(home_team, away_team)
    prob_home_win, prob_away_win, prob_draw = simulate_match(lambda_home, lambda_away)
    
    if prob_home_win > prob_away_win and prob_home_win > prob_draw:
        points[home_team] += 3
    elif prob_away_win > prob_home_win and prob_away_win > prob_draw:
        points[away_team] += 3
    else:
        points[home_team] += 1
        points[away_team] += 1

# Print final points
for team, pts in sorted(points.items(), key=lambda item: item[1], reverse=True):
    print(f"{team}: {pts} points"

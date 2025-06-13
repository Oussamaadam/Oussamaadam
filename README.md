from nba_api.stats.endpoints import DraftHistory, CommonPlayerInfo
import pandas as pd

def get_draft_info(year, pick_number):
    # Get full draft history
    draft = DraftHistory().get_data_frames()[0]

    # Convert column types for consistency
    draft['SEASON'] = draft['SEASON'].astype(str)
    draft['OVERALL_PICK'] = pd.to_numeric(draft['OVERALL_PICK'], errors='coerce')

    # Debug: Check data from that year
    filtered_year = draft[draft['SEASON'] == str(year)]
    if filtered_year.empty:
        print(f"No draft data found for the year {year}.")
        return
    else:
        print(f"Total picks in {year}: {len(filtered_year)}")

    # Filter for the pick number
    player_row = filtered_year[filtered_year['OVERALL_PICK'] == int(pick_number)]

    if player_row.empty:
        print(f"No data found for pick {pick_number} in {year}.")
        return

    for index, row in player_row.iterrows():
        player_id = row['PERSON_ID']
        name = row['PLAYER_NAME']
        draft_number = row['OVERALL_PICK']
        team = row['TEAM_NAME']
        round_number = row['ROUND_NUMBER']

        # Get detailed player info
        info = CommonPlayerInfo(player_id=player_id).get_data_frames()[0]
        height = info['HEIGHT'].values[0]
        birthdate = pd.to_datetime(info['BIRTHDATE'].values[0])
        age_at_draft = int(year) - birthdate.year

         print(f"\nName: {name}")
        print(f"Draft Number: {draft_number}")
        print(f"Age at Draft: {age_at_draft}")
        print(f"Height: {height}")
        print(f"Team Drafted To: {team}")
        print(f"Round Drafted: {round_number}")
        print("-" * 30)

# Example use
get_draft_info("2020", 1)

Total picks in 2020: 60

Name: Anthony Edwards
Draft Number: 1
Age at Draft: 19
Height: 6-4
Team Drafted To: Timberwolves
Round Drafted: 1

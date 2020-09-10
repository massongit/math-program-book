■プロ野球選手身長体重.csv, 2017プロ野球スコア.csv

NPB公式サイトより取得
http://npb.jp/teams/



# プロ野球スコアをパースした何か
with open('npb_scores.csv') as f:
    next(f)
    prev_date = None
    records = []
    for line in f:
        cols = line.strip().split(',')
        card = cols[1].split(' ')
        if len(card) < 2:
            continue
        score = card[1].split('-')
        if len(score) < 2:
            continue
        stadium = cols[2].split(' ')
        hour, minute = stadium[1].split(':')
        if len(cols[0]) == 0:
            date = prev_date
            date = date.replace(hour=int(hour), minute=int(minute))
        else:
            date = cols[0].split('（')[0]
            date = datetime.datetime(2017, int(date.split('/')[0]), int(date.split('/')[1]))
            prev_date = date
            date = date.replace(hour=int(hour), minute=int(minute))
        records.append({
            'date': date.strftime('%Y%m%d'),
            'time': date.strftime('%H%M'),
            'home_team': card[0],
            'visitor_team': card[2],
            'home_score': int(score[0]),
            'visitor_score': int(score[1]),
            'stadium': stadium[0]
        })
df = pd.DataFrame(records)
df.to_csv('npb_scores.tsv', sep='\t', index=False, columns=['date', 'time', 'home_team', 'visitor_team', 'home_score', 'visitor_score', 'stadium'])



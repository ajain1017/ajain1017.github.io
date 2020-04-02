---
layout: post
title:      "My First Attempt at a Kaggle Competition"
date:       2020-03-21 13:01:00 -0400
permalink:  my_first_attempt_at_a_kaggle_competition
---


Over the past two weeks, I hit a major stepping stone in my data science career by attempting my first Kaggle Competition for my classification project. I found it fitting that my first attempt would be related to basketball as that sport has been a passion of mine for some time and the reason I decided to join the Flatiron School Data Science Program. The goal of the competition was to use past NCAA tournament and NCAA Regular Season data to predict the probability the team with the smaller ID number would win for all possible matchups in this upcoming year's tournament. My goal was to use a classifier that would place each matchup either a win or a loss for the lower ID team, and show the probability it would land in either class. However, the competition fell short as the tournament was cancelled (or postponed? I can't tell what to make of their response) due to the COVID-19 virus spreading around. So, I found myself focusing on predicting first round matchups, specifically focussing on upsets for the previous three years.

In order get a realtive statistic of performance by one team, instead of taking rolling average of hard game stats, I wanted to compute rolling averages of difference of those hard game stats along with a few advanced metrics that are used mostly in NBA but can be adapted for NCAA. As the goal is to predict the tournament games using available averages from games played before the date of the game itself, I had to keep the sum of the statistics in a seperate dataframe of all teams and feed the averages into the train dataset dataframe. The function used can be seen below:

```
def get_stats(test):
    for i in [2014, 2015, 2016]:
        df20['Wins' + str(i)] = 0
        df20['Losses' + str(i)] = 0
        df20['TmPoints' + str(i)] = 0
        df20['OppPoints' + str(i)] = 0
        
        df20['Scorediff' + str(i)] = 0
        df20['2FG%diff' + str(i)] = 0
        df20['3FG%diff' + str(i)] = 0
        df20['FT%diff' + str(i)] = 0
        df20['2FGMdiff' + str(i)] = 0
        df20['3FGMdiff' + str(i)] = 0
        df20['FTMdiff' + str(i)] = 0
        df20['2FGAdiff' + str(i)] = 0
        df20['3FGAdiff' + str(i)] = 0
        df20['FTAdiff' + str(i)] = 0
        df20['ORdiff' + str(i)] = 0
        df20['DRdiff' + str(i)] = 0
        df20['TRdiff' + str(i)] = 0
        df20['Adiff' + str(i)] = 0
        df20['TOdiff' + str(i)] = 0
        df20['STdiff' + str(i)] = 0
        df20['BLKdiff' + str(i)] = 0
        df20['PFdiff' + str(i)] = 0
        df20['TS' + str(i)] = 0
        df20['Pace' + str(i)] = 0
        df20['ORTG' + str(i)] = 0        
        df20['DRTG' + str(i)] = 0
        df20['NRTG' + str(i)] = 0
                 
        data = test[test.Season == i]
        data.reset_index(inplace = True)
        data.rename(columns = {'index': 'bar'}, inplace = True)
        fakeindex = data.bar[0]
        
        for idx, x in enumerate(data.WTeamID):
            idxl = df20[df20['TeamID'] == data['LTeamID'].iloc[idx]].index.tolist()[0]
            idxw = df20[df20['TeamID'] == data['WTeamID'].iloc[idx]].index.tolist()[0]

            gamesw = (df20['Wins' + str(i)].iloc[idxw] + df20['Losses' + str(i)].iloc[idxw])
            gamesl = (df20['Wins' + str(i)].iloc[idxl] + df20['Losses' + str(i)].iloc[idxl])
            
            df20.loc[idxw, 'Scorediff' + str(i)] += data['WScore'].iloc[idx] - data['LScore'].iloc[idx]
            df20.loc[idxw, '2FG%diff' + str(i)] += ((data['WFGM'].iloc[idx] - data['WFGM3'].iloc[idx])/(data['WFGA'].iloc[idx] - data['WFGA3'].iloc[idx])) - ((data['LFGM'].iloc[idx] - data['LFGM3'].iloc[idx])/(data['LFGA'].iloc[idx] - data['LFGA3'].iloc[idx]))
            df20.loc[idxw, '3FG%diff' + str(i)] += (data['WFGM3'].iloc[idx]/data['WFGA3'].iloc[idx]) - (data['LFGM3'].iloc[idx]/data['LFGA3'].iloc[idx])
            df20.loc[idxw, 'FT%diff' + str(i)] += (data['WFTM'].iloc[idx]/data['WFTA'].iloc[idx]) - (data['LFTM'].iloc[idx]/data['LFTA'].iloc[idx])
            df20.loc[idxw, '2FGMdiff' + str(i)] += ((data['WFGM'].iloc[idx] - data['WFGM3'].iloc[idx]) - (data['LFGM'].iloc[idx] - data['LFGM3'].iloc[idx]))
            df20.loc[idxw, '3FGMdiff' + str(i)] += (data['WFGM3'].iloc[idx] - data['LFGM3'].iloc[idx])
            df20.loc[idxw, 'FTMdiff' + str(i)] += (data['WFTM'].iloc[idx] - data['LFTM'].iloc[idx])
            df20.loc[idxw, '2FGAdiff' + str(i)] += ((data['WFGA'].iloc[idx] - data['WFGA3'].iloc[idx]) - (data['LFGA'].iloc[idx] - data['LFGA3'].iloc[idx]))
            df20.loc[idxw, '3FGAdiff' + str(i)] += (data['WFGA3'].iloc[idx] - data['LFGA3'].iloc[idx])
            df20.loc[idxw, 'FTAdiff' + str(i)] += (data['WFTA'].iloc[idx] - data['LFTA'].iloc[idx])
            df20.loc[idxw, 'ORdiff' + str(i)] += (data['WOR'].iloc[idx] - data['LOR'].iloc[idx])
            df20.loc[idxw, 'DRdiff' + str(i)] += (data['WDR'].iloc[idx] - data['LDR'].iloc[idx])
            df20.loc[idxw, 'TRdiff' + str(i)] += (data['WOR'].iloc[idx] + data['WDR'].iloc[idx]) - (data['LOR'].iloc[idx] + data['LDR'].iloc[idx])
            df20.loc[idxw, 'Adiff' + str(i)] += (data['WAst'].iloc[idx] - data['LAst'].iloc[idx])
            df20.loc[idxw, 'TOdiff' + str(i)] += (data['WTO'].iloc[idx] - data['LTO'].iloc[idx])
            df20.loc[idxw, 'STdiff' + str(i)] += (data['WStl'].iloc[idx] - data['LStl'].iloc[idx])
            df20.loc[idxw, 'BLKdiff' + str(i)] += (data['WBlk'].iloc[idx] - data['LBlk'].iloc[idx])
            df20.loc[idxw, 'PFdiff' + str(i)] += (data['WPF'].iloc[idx] - data['LPF'].iloc[idx])
            df20.loc[idxw, 'Wins' + str(i)] += 1
            df20.loc[idxw, 'TmPoints' + str(i)] += data.WScore[idx]
            df20.loc[idxw, 'OppPoints' + str(i)] += data.LScore[idx]
            df20.loc[idxw, 'TS' + str(i)] += data.WScore.iloc[idx]/(2*(data.WFGA.iloc[idx]+(0.475*data.WFTA.iloc[idx])))
            poss = (0.5*(data.WFGA.iloc[idx]+(0.475*data.WFTA.iloc[idx])-data.WOR.iloc[idx]+data.WTO.iloc[idx]))+0.5*(data.LFGA.iloc[idx]+(0.475*data.LFTA.iloc[idx])-data.LOR.iloc[idx]+data.LTO.iloc[idx])
            df20.loc[idxw, 'Pace' + str(i)] += 40*(poss/(0.2*(40 + (data.NumOT.iloc[idx]*5))))
            df20.loc[idxw, 'ORTG' + str(i)] += data.WScore.iloc[idx] / poss
            df20.loc[idxw, 'DRTG' + str(i)] += data.LScore.iloc[idx] / poss
            df20.loc[idxw, 'NRTG' + str(i)] += (data.WScore.iloc[idx] / poss) - (data.LScore.iloc[idx] / poss)

            df20.loc[idxl, 'Scorediff' + str(i)] += -1 * (data['WScore'].iloc[idx] - data['LScore'].iloc[idx])
            df20.loc[idxl, '2FG%diff' + str(i)] += -1 * ((data['WFGM'].iloc[idx] - data['WFGM3'].iloc[idx])/(data['WFGA'].iloc[idx] - data['WFGA3'].iloc[idx])) - ((data['LFGM'].iloc[idx] - data['LFGM3'].iloc[idx])/(data['LFGA'].iloc[idx] - data['LFGA3'].iloc[idx]))
            df20.loc[idxl, '3FG%diff' + str(i)] += -1 * (data['WFGM3'].iloc[idx]/data['WFGA3'].iloc[idx]) - (data['LFGM3'].iloc[idx]/data['LFGA3'].iloc[idx])
            df20.loc[idxl, 'FT%diff' + str(i)] += -1 * (data['WFTM'].iloc[idx]/data['WFTA'].iloc[idx]) - (data['LFTM'].iloc[idx]/data['LFTA'].iloc[idx])
            df20.loc[idxl, '2FGMdiff' + str(i)] += -1 * ((data['WFGM'].iloc[idx] - data['WFGM3'].iloc[idx]) - (data['LFGM'].iloc[idx] - data['LFGM3'].iloc[idx]))
            df20.loc[idxl, '3FGMdiff' + str(i)] += -1 * (data['WFGM3'].iloc[idx] - data['LFGM3'].iloc[idx])
            df20.loc[idxl, 'FTMdiff' + str(i)] += -1 * (data['WFTM'].iloc[idx] - data['LFTM'].iloc[idx])
            df20.loc[idxl, '2FGAdiff' + str(i)] += -1 * ((data['WFGA'].iloc[idx] - data['WFGA3'].iloc[idx]) - (data['LFGA'].iloc[idx] - data['LFGA3'].iloc[idx]))
            df20.loc[idxl, '3FGAdiff' + str(i)] += -1 * (data['WFGA3'].iloc[idx] - data['LFGA3'].iloc[idx])
            df20.loc[idxl, 'FTAdiff' + str(i)] += -1 * (data['WFTA'].iloc[idx] - data['LFTA'].iloc[idx])
            df20.loc[idxl, 'ORdiff' + str(i)] += -1 * (data['WOR'].iloc[idx] - data['LOR'].iloc[idx])
            df20.loc[idxl, 'DRdiff' + str(i)] += -1 * (data['WDR'].iloc[idx] - data['LDR'].iloc[idx])
            df20.loc[idxl, 'TRdiff' + str(i)] += -1 * (data['WOR'].iloc[idx] + data['WDR'].iloc[idx]) - (data['LOR'].iloc[idx] + data['LDR'].iloc[idx])
            df20.loc[idxl, 'Adiff' + str(i)] += -1 * (data['WAst'].iloc[idx] - data['LAst'].iloc[idx])
            df20.loc[idxl, 'TOdiff' + str(i)] += -1 * (data['WTO'].iloc[idx] - data['LTO'].iloc[idx])
            df20.loc[idxl, 'STdiff' + str(i)] += -1 * (data['WStl'].iloc[idx] - data['LStl'].iloc[idx])
            df20.loc[idxl, 'BLKdiff' + str(i)] += -1 * (data['WBlk'].iloc[idx] - data['LBlk'].iloc[idx])
            df20.loc[idxl, 'PFdiff' + str(i)] += -1 * (data['WPF'].iloc[idx] - data['LPF'].iloc[idx])
            df20.loc[idxl, 'Losses' + str(i)] += 1
            df20.loc[idxl, 'TmPoints' + str(i)] += data.LScore[idx]
            df20.loc[idxl, 'OppPoints' + str(i)] += data.WScore[idx]
            df20.loc[idxl, 'TS' + str(i)] += data.LScore.iloc[idx]/(2*(data.LFGA.iloc[idx]+(0.475*data.LFTA.iloc[idx])))
            df20.loc[idxl, 'Pace' + str(i)] += 40*(poss/(0.2*(40 + (data.NumOT.iloc[idx]*5))))
            df20.loc[idxl, 'ORTG' + str(i)] += data.LScore.iloc[idx] / poss
            df20.loc[idxl, 'DRTG' + str(i)] += data.WScore.iloc[idx] / poss
            df20.loc[idxl, 'NRTG' + str(i)] += (data.LScore.iloc[idx] / poss) - (data.WScore.iloc[idx] / poss)
            
            print(idx + fakeindex)        
        
    for i in [2017, 2018, 2019]:        
        df20['Wins' + str(i)] = 0
        df20['Losses' + str(i)] = 0
        df20['TmPoints' + str(i)] = 0
        df20['OppPoints' + str(i)] = 0
        
        df20['Scorediff' + str(i)] = 0
        df20['2FG%diff' + str(i)] = 0
        df20['3FG%diff' + str(i)] = 0
        df20['FT%diff' + str(i)] = 0
        df20['2FGMdiff' + str(i)] = 0
        df20['3FGMdiff' + str(i)] = 0
        df20['FTMdiff' + str(i)] = 0
        df20['2FGAdiff' + str(i)] = 0
        df20['3FGAdiff' + str(i)] = 0
        df20['FTAdiff' + str(i)] = 0
        df20['ORdiff' + str(i)] = 0
        df20['DRdiff' + str(i)] = 0
        df20['TRdiff' + str(i)] = 0
        df20['Adiff' + str(i)] = 0
        df20['TOdiff' + str(i)] = 0
        df20['STdiff' + str(i)] = 0
        df20['BLKdiff' + str(i)] = 0
        df20['PFdiff' + str(i)] = 0
        df20['TS' + str(i)] = 0
        df20['Pace' + str(i)] = 0
        df20['ORTG' + str(i)] = 0        
        df20['DRTG' + str(i)] = 0
        df20['NRTG' + str(i)] = 0
                 
        data = test[test.Season == i]
        data.reset_index(inplace = True)
        data.rename(columns = {'index': 'bar'}, inplace = True)
        fakeindex = data.bar[0]
        
        for idx, x in enumerate(data.WTeamID):
            idxl = df20[df20['TeamID'] == data['LTeamID'].iloc[idx]].index.tolist()[0]
            idxw = df20[df20['TeamID'] == data['WTeamID'].iloc[idx]].index.tolist()[0]

            gamesw = (df20['Wins' + str(i)].iloc[idxw] + df20['Losses' + str(i)].iloc[idxw])
            gamesl = (df20['Wins' + str(i)].iloc[idxl] + df20['Losses' + str(i)].iloc[idxl])

            test.loc[idx + fakeindex, 'WScorediff'] = 0 if gamesw == 0 else df20['Scorediff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'W2FG%diff'] = 0 if gamesw == 0 else df20['2FG%diff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'W3FG%diff'] = 0 if gamesw == 0 else df20['3FG%diff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WFT%diff'] = 0 if gamesw == 0 else df20['FT%diff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'W2FGMdiff'] = 0 if gamesw == 0 else df20['2FGMdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'W3FGMdiff'] = 0 if gamesw == 0 else df20['3FGMdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WFTMdiff'] = 0 if gamesw == 0 else df20['FTMdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'W2FGAdiff'] = 0 if gamesw == 0 else df20['2FGAdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'W3FGAdiff'] = 0 if gamesw == 0 else df20['3FGAdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WFTAdiff'] = 0 if gamesw == 0 else df20['FTAdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WORdiff'] = 0 if gamesw == 0 else df20['ORdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WDRdiff'] = 0 if gamesw == 0 else df20['DRdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WTRdiff'] = 0 if gamesw == 0 else df20['TRdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WAdiff'] = 0 if gamesw == 0 else df20['Adiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WTOdiff'] = 0 if gamesw == 0 else df20['TOdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WSTdiff'] = 0 if gamesw == 0 else df20['STdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WBLKdiff'] = 0 if gamesw == 0 else df20['BLKdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WPFdiff'] = 0 if gamesw == 0 else df20['PFdiff' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WTS'] = 0 if gamesw == 0 else df20['TS' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WPace'] = 0 if gamesw == 0 else df20['Pace' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WORTG'] = 0 if gamesw == 0 else df20['ORTG' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WDRTG'] = 0 if gamesw == 0 else df20['DRTG' + str(i)].iloc[idxw]/gamesw
            test.loc[idx + fakeindex, 'WNRTG'] = 0 if gamesw == 0 else df20['NRTG' + str(i)].iloc[idxw]/gamesw
            
            
            test.loc[idx + fakeindex, 'LScorediff'] = 0 if gamesl == 0 else df20['Scorediff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'L2FG%diff'] = 0 if gamesl == 0 else df20['2FG%diff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'L3FG%diff'] = 0 if gamesl == 0 else df20['3FG%diff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LFT%diff'] = 0 if gamesl == 0 else df20['FT%diff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'L2FGMdiff'] = 0 if gamesl == 0 else df20['2FGMdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'L3FGMdiff'] = 0 if gamesl == 0 else df20['3FGMdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LFTMdiff'] = 0 if gamesl == 0 else df20['FTMdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'L2FGAdiff'] = 0 if gamesl == 0 else df20['2FGAdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'L3FGAdiff'] = 0 if gamesl == 0 else df20['3FGAdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LFTAdiff'] = 0 if gamesl == 0 else df20['FTAdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LORdiff'] = 0 if gamesl == 0 else df20['ORdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LDRdiff'] = 0 if gamesl == 0 else df20['DRdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LTRdiff'] = 0 if gamesl == 0 else df20['TRdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LAdiff'] = 0 if gamesl == 0 else df20['Adiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LTOdiff'] = 0 if gamesl == 0 else df20['TOdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LSTdiff'] = 0 if gamesl == 0 else df20['STdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LBLKdiff'] = 0 if gamesl == 0 else df20['BLKdiff' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LPFdiff'] = 0 if gamesl == 0 else df20['PFdiff' + str(i)].iloc[idxl]/gamesl            
            test.loc[idx + fakeindex, 'LTS'] = 0 if gamesw == 0 else df20['TS' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LPace'] = 0 if gamesw == 0 else df20['Pace' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LORTG'] = 0 if gamesw == 0 else df20['ORTG' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LDRTG'] = 0 if gamesw == 0 else df20['DRTG' + str(i)].iloc[idxl]/gamesl
            test.loc[idx + fakeindex, 'LNRTG'] = 0 if gamesw == 0 else df20['NRTG' + str(i)].iloc[idxl]/gamesl            
            test.loc[idx + fakeindex, 'WTmPoints'] = df20['TmPoints' + str(i)][idxw]
            test.loc[idx + fakeindex, 'WOppPoints'] = df20['OppPoints' + str(i)][idxw]
            test.loc[idx + fakeindex, 'LTmPoints'] = df20['TmPoints' + str(i)][idxl]
            test.loc[idx + fakeindex, 'LOppPoints'] = df20['OppPoints' + str(i)][idxl]


            test.loc[idx + fakeindex, 'WWinsCount'] = df20['Wins' + str(i)].iloc[idxw]
            test.loc[idx + fakeindex, 'LWinsCount'] = df20['Wins' + str(i)].iloc[idxl]
            test.loc[idx + fakeindex, 'WLossesCount'] = df20['Losses' + str(i)].iloc[idxw]
            test.loc[idx + fakeindex, 'LLossesCount'] = df20['Losses' + str(i)].iloc[idxl]
            test.loc[idx + fakeindex, 'WWPyth'] = 0 if gamesw == 0 else (gamesw * (math.pow(df20['TmPoints' + str(i)][idxw],  9) / sum([math.pow(df20['TmPoints' + str(i)][idxw],  9), math.pow(df20['OppPoints' + str(i)][idxw],  9)])))
            test.loc[idx + fakeindex, 'LWPyth'] = 0 if gamesl == 0 else (gamesl * (math.pow(df20['TmPoints' + str(i)][idxl],  9) / sum([math.pow(df20['TmPoints' + str(i)][idxl],  9), math.pow(df20['OppPoints' + str(i)][idxl],  9)])))
            test.loc[idx + fakeindex, 'WRealWinsDiff'] = test.WWinsCount.iloc[idx + fakeindex] - test.WWPyth.iloc[idx + fakeindex]
            test.loc[idx + fakeindex, 'LRealWinsDiff'] = test.LWinsCount.iloc[idx + fakeindex] - test.LWPyth.iloc[idx + fakeindex]

            test.loc[idx + fakeindex, 'Existencediff'] = df20[str(data.Season[idx])][idxw] - df20[str(data.Season[idx])][idxl]

            proxy = df17[df17['Season'] == data.Season[idx]]
            proxy = proxy[proxy['RankingDayNum'] <= data['DayNum'][idx]]
            proxyw = proxy[proxy['TeamID'] == x]
            proxyl = proxy[proxy['TeamID'] == data['LTeamID'][idx]]
            if proxyw.empty == True:
                pass
            elif proxyl.empty == True:
                pass
            else:
                proxyw = proxyw[proxyw['RankingDayNum'] == proxyw['RankingDayNum'].iloc[-1]]
                proxyl = proxyl[proxyl['RankingDayNum'] == proxyl['RankingDayNum'].iloc[-1]]
                test.loc[idx + fakeindex, 'Rankdiff'] = proxyl.OrdinalRank.mean() - proxyw.OrdinalRank.mean()

            if data.WLoc.iloc[idx] == 'H':
                test.loc[idx + fakeindex, 'WH'] = 1
                test.loc[idx + fakeindex, 'WA'] = 0
                test.loc[idx + fakeindex, 'WN'] = 0
                test.loc[idx + fakeindex, 'LH'] = 0
                test.loc[idx + fakeindex, 'LA'] = 1
                test.loc[idx + fakeindex, 'LN'] = 0
            elif data.WLoc.iloc[idx] == 'A':
                test.loc[idx + fakeindex, 'WH'] = 0
                test.loc[idx + fakeindex, 'WA'] = 1
                test.loc[idx + fakeindex, 'WN'] = 0
                test.loc[idx + fakeindex, 'LH'] = 1
                test.loc[idx + fakeindex, 'LA'] = 0
                test.loc[idx + fakeindex, 'LN'] = 0
            elif data.WLoc.iloc[idx] == 'N':
                test.loc[idx + fakeindex, 'WH'] = 0
                test.loc[idx + fakeindex, 'WA'] = 0
                test.loc[idx + fakeindex, 'WN'] = 1
                test.loc[idx + fakeindex, 'LH'] = 0
                test.loc[idx + fakeindex, 'LA'] = 0
                test.loc[idx + fakeindex, 'LN'] = 1


            coachdiff = df15[df15['Season'] == data['Season'][idx]]
            coachdiffw = coachdiff[coachdiff['TeamID'] == x]
            coachdiffl = coachdiff[coachdiff['TeamID'] == data['LTeamID'][idx]]

            for r, s in enumerate(coachdiffw.LastDayNum):
                if data.DayNum.iloc[idx] > s:
                    pass
                elif data.DayNum.iloc[idx] <= s:
                    daysw = coachdiffw.CoachTenure.iloc[r] - (s - data.DayNum.iloc[idx])

            for r, s in enumerate(coachdiffl.LastDayNum):
                if data.DayNum.iloc[idx] > s:
                    pass
                elif data.DayNum.iloc[idx] <= s:
                    daysl = coachdiffl.CoachTenure.iloc[r] - (s - data.DayNum.iloc[idx])

            test.loc[idx + fakeindex, 'Coachdiff'] = daysw - daysl
            
            df20.loc[idxw, 'Scorediff' + str(i)] += data['WScore'].iloc[idx] - data['LScore'].iloc[idx]
            df20.loc[idxw, '2FG%diff' + str(i)] += ((data['WFGM'].iloc[idx] - data['WFGM3'].iloc[idx])/(data['WFGA'].iloc[idx] - data['WFGA3'].iloc[idx])) - ((data['LFGM'].iloc[idx] - data['LFGM3'].iloc[idx])/(data['LFGA'].iloc[idx] - data['LFGA3'].iloc[idx]))
            df20.loc[idxw, '3FG%diff' + str(i)] += (data['WFGM3'].iloc[idx]/data['WFGA3'].iloc[idx]) - (data['LFGM3'].iloc[idx]/data['LFGA3'].iloc[idx])
            df20.loc[idxw, 'FT%diff' + str(i)] += (data['WFTM'].iloc[idx]/data['WFTA'].iloc[idx]) - (data['LFTM'].iloc[idx]/data['LFTA'].iloc[idx])
            df20.loc[idxw, '2FGMdiff' + str(i)] += ((data['WFGM'].iloc[idx] - data['WFGM3'].iloc[idx]) - (data['LFGM'].iloc[idx] - data['LFGM3'].iloc[idx]))
            df20.loc[idxw, '3FGMdiff' + str(i)] += (data['WFGM3'].iloc[idx] - data['LFGM3'].iloc[idx])
            df20.loc[idxw, 'FTMdiff' + str(i)] += (data['WFTM'].iloc[idx] - data['LFTM'].iloc[idx])
            df20.loc[idxw, '2FGAdiff' + str(i)] += ((data['WFGA'].iloc[idx] - data['WFGA3'].iloc[idx]) - (data['LFGA'].iloc[idx] - data['LFGA3'].iloc[idx]))
            df20.loc[idxw, '3FGAdiff' + str(i)] += (data['WFGA3'].iloc[idx] - data['LFGA3'].iloc[idx])
            df20.loc[idxw, 'FTAdiff' + str(i)] += (data['WFTA'].iloc[idx] - data['LFTA'].iloc[idx])
            df20.loc[idxw, 'ORdiff' + str(i)] += (data['WOR'].iloc[idx] - data['LOR'].iloc[idx])
            df20.loc[idxw, 'DRdiff' + str(i)] += (data['WDR'].iloc[idx] - data['LDR'].iloc[idx])
            df20.loc[idxw, 'TRdiff' + str(i)] += (data['WOR'].iloc[idx] + data['WDR'].iloc[idx]) - (data['LOR'].iloc[idx] + data['LDR'].iloc[idx])
            df20.loc[idxw, 'Adiff' + str(i)] += (data['WAst'].iloc[idx] - data['LAst'].iloc[idx])
            df20.loc[idxw, 'TOdiff' + str(i)] += (data['WTO'].iloc[idx] - data['LTO'].iloc[idx])
            df20.loc[idxw, 'STdiff' + str(i)] += (data['WStl'].iloc[idx] - data['LStl'].iloc[idx])
            df20.loc[idxw, 'BLKdiff' + str(i)] += (data['WBlk'].iloc[idx] - data['LBlk'].iloc[idx])
            df20.loc[idxw, 'PFdiff' + str(i)] += (data['WPF'].iloc[idx] - data['LPF'].iloc[idx])
            df20.loc[idxw, 'Wins' + str(i)] += 1
            df20.loc[idxw, 'TmPoints' + str(i)] += data.WScore[idx]
            df20.loc[idxw, 'OppPoints' + str(i)] += data.LScore[idx]
            df20.loc[idxw, 'TS' + str(i)] += data.WScore.iloc[idx]/(2*(data.WFGA.iloc[idx]+(0.475*data.WFTA.iloc[idx])))
            poss = (0.5*(data.WFGA.iloc[idx]+(0.475*data.WFTA.iloc[idx])-data.WOR.iloc[idx]+data.WTO.iloc[idx]))+0.5*(data.LFGA.iloc[idx]+(0.475*data.LFTA.iloc[idx])-data.LOR.iloc[idx]+data.LTO.iloc[idx])
            df20.loc[idxw, 'Pace' + str(i)] += 40*(poss/(0.2*(40 + (data.NumOT.iloc[idx]*5))))
            df20.loc[idxw, 'ORTG' + str(i)] += data.WScore.iloc[idx] / poss
            df20.loc[idxw, 'DRTG' + str(i)] += data.LScore.iloc[idx] / poss
            df20.loc[idxw, 'NRTG' + str(i)] += (data.WScore.iloc[idx] / poss) - (data.LScore.iloc[idx] / poss)
            

            df20.loc[idxl, 'Scorediff' + str(i)] += -1 * (data['WScore'].iloc[idx] - data['LScore'].iloc[idx])
            df20.loc[idxl, '2FG%diff' + str(i)] += -1 * ((data['WFGM'].iloc[idx] - data['WFGM3'].iloc[idx])/(data['WFGA'].iloc[idx] - data['WFGA3'].iloc[idx])) - ((data['LFGM'].iloc[idx] - data['LFGM3'].iloc[idx])/(data['LFGA'].iloc[idx] - data['LFGA3'].iloc[idx]))
            df20.loc[idxl, '3FG%diff' + str(i)] += -1 * (data['WFGM3'].iloc[idx]/data['WFGA3'].iloc[idx]) - (data['LFGM3'].iloc[idx]/data['LFGA3'].iloc[idx])
            df20.loc[idxl, 'FT%diff' + str(i)] += -1 * (data['WFTM'].iloc[idx]/data['WFTA'].iloc[idx]) - (data['LFTM'].iloc[idx]/data['LFTA'].iloc[idx])
            df20.loc[idxl, '2FGMdiff' + str(i)] += -1 * ((data['WFGM'].iloc[idx] - data['WFGM3'].iloc[idx]) - (data['LFGM'].iloc[idx] - data['LFGM3'].iloc[idx]))
            df20.loc[idxl, '3FGMdiff' + str(i)] += -1 * (data['WFGM3'].iloc[idx] - data['LFGM3'].iloc[idx])
            df20.loc[idxl, 'FTMdiff' + str(i)] += -1 * (data['WFTM'].iloc[idx] - data['LFTM'].iloc[idx])
            df20.loc[idxl, '2FGAdiff' + str(i)] += -1 * ((data['WFGA'].iloc[idx] - data['WFGA3'].iloc[idx]) - (data['LFGA'].iloc[idx] - data['LFGA3'].iloc[idx]))
            df20.loc[idxl, '3FGAdiff' + str(i)] += -1 * (data['WFGA3'].iloc[idx] - data['LFGA3'].iloc[idx])
            df20.loc[idxl, 'FTAdiff' + str(i)] += -1 * (data['WFTA'].iloc[idx] - data['LFTA'].iloc[idx])
            df20.loc[idxl, 'ORdiff' + str(i)] += -1 * (data['WOR'].iloc[idx] - data['LOR'].iloc[idx])
            df20.loc[idxl, 'DRdiff' + str(i)] += -1 * (data['WDR'].iloc[idx] - data['LDR'].iloc[idx])
            df20.loc[idxl, 'TRdiff' + str(i)] += -1 * (data['WOR'].iloc[idx] + data['WDR'].iloc[idx]) - (data['LOR'].iloc[idx] + data['LDR'].iloc[idx])
            df20.loc[idxl, 'Adiff' + str(i)] += -1 * (data['WAst'].iloc[idx] - data['LAst'].iloc[idx])
            df20.loc[idxl, 'TOdiff' + str(i)] += -1 * (data['WTO'].iloc[idx] - data['LTO'].iloc[idx])
            df20.loc[idxl, 'STdiff' + str(i)] += -1 * (data['WStl'].iloc[idx] - data['LStl'].iloc[idx])
            df20.loc[idxl, 'BLKdiff' + str(i)] += -1 * (data['WBlk'].iloc[idx] - data['LBlk'].iloc[idx])
            df20.loc[idxl, 'PFdiff' + str(i)] += -1 * (data['WPF'].iloc[idx] - data['LPF'].iloc[idx])
            df20.loc[idxl, 'Losses' + str(i)] += 1
            df20.loc[idxl, 'TmPoints' + str(i)] += data.LScore[idx]
            df20.loc[idxl, 'OppPoints' + str(i)] += data.WScore[idx]
            df20.loc[idxl, 'TS' + str(i)] += data.LScore.iloc[idx]/(2*(data.LFGA.iloc[idx]+(0.475*data.LFTA.iloc[idx])))
            df20.loc[idxl, 'Pace' + str(i)] += 40*(poss/(0.2*(40 + (data.NumOT.iloc[idx]*5))))
            df20.loc[idxl, 'ORTG' + str(i)] += data.LScore.iloc[idx] / poss
            df20.loc[idxl, 'DRTG' + str(i)] += data.WScore.iloc[idx] / poss
            df20.loc[idxl, 'NRTG' + str(i)] += (data.LScore.iloc[idx] / poss) - (data.WScore.iloc[idx] / poss)
            
            print(idx + fakeindex)
```

I chose to use only seasons 2017, 2018 and 2019 because college players are eligible to leave their university to play professionally after one year. This results in a lot of change in roster makeup over the years, but the maximum number of years a player can play on a team is still 4 years. Therefore, our first goal of predicting 2020 tournament matchups coudl have been done using the 2017-2019 data. Regardless, I was able to go through each games event by time to find in game statistics for the years I am looking to predict (2017-2019) as well. This was a very cool and detailed dataset that was available through kaggle. Unfortunately, due to some NuLL values in positioning for the year 2017 and 2018, I wasn't able to distinguish between shooting zones on a court as I wanted to. Instead, I decided that shooting percentages for the first and last five minutes of each half would be beneficial to consider as basketball is a game of runs. I always say play the same from start to finish and this encompases that notion.

After I computed all my rolling averages for each game in 2017, 2018 and 2019 along with the in game event statistics, I averaged the previous three years end of season statistics for each team as well. Then, I set my target being whether the team with the lower Team ID won the game or not. In order to keep all the features representing the same teams, I rearranged the values in the rows to have the lower Team ID team be Team 1 and the higher Team ID team be Team 2.

For hypertuning my parameters and finding the best classifier to use, I used a pipeline and gridsearch for three classifiers: Logistic Regression, RandomForest and XGBoost.

For feature selection/dimensionality issues, I used PCA as it was fast and efficient regardless of it not considering the dependent variable. As lower dimensional space allows for better seeing seperation between points, we have better interpretation for classification.

As our data varies in size and most are continous, except for the six binary categorical features relating to location of game, we use a StandardScalar to standardize our datapoints to lie closer for PCA to be performed.

Lastly, we hypertune our parameters by selecting a few parameters to test for. Unfortunately, the runtime was so long that I couldn't test for all possible values of depth, number of estimators and number of components. Below, you may see the code for executing our pipeline and gridsearch:

```
random_state = 42
classifiers = [XGBClassifier(random_state = random_state),
               LogisticRegression(random_state=random_state),
               RandomForestClassifier(random_state=random_state)]

params = [
        {'clf__n_estimators': [20,50,100],
     'clf__max_depth':[3,4,5,6],
     'pca__n_components':list(range(40,101))}, ## xgboost
    
    {'clf__C':[0.001, .01, .10, 0.50, 0.75, 1.0, 10, 100],
     'pca__n_components': list(range(40,101))}, ## Logistic Regression
    
    {'clf__n_estimators': [20,50,100],
     'clf__criterion': ['entropy', 'gini'],
     'clf__max_depth':[None,3,4,5,6],
     'pca__n_components':list(range(60,101))} ## randomforestclassifier
]


pipelines1 = []
best_clfs1 = []
for clf,param in zip(classifiers,params):
    pipe = Pipeline([('scl', StandardScaler()),
                     ('pca', PCA()),
                     ('clf', clf)])
    gs = GridSearchCV(estimator=pipe,
                  param_grid=param,
                  scoring='accuracy',
                  cv=3,
                  verbose=5,
                  n_jobs=-1)
    gs.fit(X_train, y_train)
    best_clfs1.append(gs.best_estimator_)
    pipelines1.append(pipe)  
    print(clf.__class__.__name__)
    print('Best params:', gs.best_params_)
    print('Train Accuracy:',gs.best_estimator_.score(X_train, y_train))
    print('Test Accuracy:',gs.best_estimator_.score(X_test, y_test))
    print('')
```

I resulted with XGBoost being the best classifier, having a test accuracy score of over 70%. A little more hypertuning with that specific classifier might result in better scores. However, if I were to say one thing about this project, it was that taking time to gather, clean and engineer the data along with gridsearching the best parameters took longer than I've ever had to do beforehand. Believe one when they say that data collection and cleaning takes up half the time but represents 10% of the project.

Overall, this first crack at a classification project and kaggle competition (somewhat attempted), was a success in my book. I gained experience in pipeline construction and more applicable experience in creating my own dataset.

-Anu

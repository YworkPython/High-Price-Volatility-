
https://quantra.quantinsti.com/glossary/Realized-Volatility

https://github.com/gkar90/Realized-Volatility/blob/345dcb694b0e9e65362946d58c01105ac29fe8d3/Realized%20Vol.ipynb

def price_movmenet(df,state, t1, t2,year, q):
    
    df_1=df[['DateTime','{} DISPATCH_PRICE'.format(state)]].copy()
    ToD(df_1, 'DateTime')
    df_1=df_1[(df_1['Time'].astype(str) >= ('{}'.format(t1)))  & (df_1['Time'].astype(str) <= ('{}'.format(t2)))]
    df_1=df_1[(df_1['CY']==year) & (df_1['Quarter'] ==q)]
    df_1['price_change'] = df_1['{} DISPATCH_PRICE'.format(state)].diff()
    up_moves = df_1[df_1['price_change'] > 0]['price_change']
    down_moves = df_1[df_1['price_change'] < 0]['price_change']
    avg_up = up_moves.mean()
    avg_down = down_moves.mean()  # Will be negati
    avg_down_abs = down_moves.abs().mean()
    Hours_of_Up_Moves=len(up_moves)/12
    Hours_of_Down_Moves=len(down_moves)/12
    return state, year, q, avg_up,avg_down,avg_down_abs,Hours_of_Up_Moves,Hours_of_Down_Moves


import os
writer = pd.ExcelWriter(path.join(r'D:\others_Output\High Price', 'High Price Analysis_price_movement_afternoon.xlsx'),engine='xlsxwriter')

#os.chdir(r'C:\Users\ygu\Documents\ES\High Price')
for state in ['QLD1','NSW1','VIC1','SA1','TAS1']:
    df_f=pd.DataFrame(columns=['State','Year','Quarter','avg_up','avg_down','avg_down_abs','Hours_of_Up_Moves','Hours_of_Down_Moves'])
    for year in range(2019, 2026):
        for q in range(1,5):
            print(state,year,q)
            df_f.loc[len(df_f)]=price_movmenet(df,state,'12:00:00','15:30:00',year,q)
    workbook=writer.book
    worksheet=workbook.add_worksheet(state)
    writer.sheets[state] = worksheet
    df_f.to_excel(writer,sheet_name=state,startrow=0 , startcol=0,index=False,float_format="%.1f")

writer.close()

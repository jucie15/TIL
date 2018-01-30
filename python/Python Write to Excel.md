## Python Write to Excel

1. ### 목표

   1. ##### 파이썬 데이터를 Excel파일로 출력한다.

2. ### TroubleShooting

   1. ##### Pandas `ExcelWriter`를 이용한 방법

      ```Python
      import pandas as pd

      df = pd.DataFrame(columns=('01-17', '18-31', '01-10'), 
                     index=('회원가입수', '첫구매건수', '구매금액', '쿠폰사용수', '쿠폰사용구매금액'))

      writer = pd.ExcelWriter('data/12_new_member_data.xlsx')
      df.to_excel(writer, sheet_name='Sheet1')
      writer.save()
      ```

   2. ##### `xlsxwriter` 모듈을 이용한 방법

      ```python
      import xlsxwriter

      # Create an new Excel file and add a worksheet.
      workbook = xlsxwriter.Workbook('data/member_list.xlsx')
      worksheet = workbook.add_worksheet()

      # Widen the first column to make the text clearer.
      worksheet.set_column('B:B', 20)
      worksheet.write('A1', '이름')
      worksheet.write('B1', '번호')

      row = 1
      for member in member_list:
          worksheet.write(row, 0, member.name)
          worksheet.write(row, 1, member.phone)
          row += 1
          
      workbook.close()
      ```

      ​




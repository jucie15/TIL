## Admin-actions with custom form page

1. #### 구현 목표

   1. ##### Admin page에 action을 추가한다.

   2. ##### action에 `CustomForm`과 `CustomPage`를 중간에 끼고 작동하게 한다.

2. #### Refer

   - ##### [중간 페이지 껴서 액션 추가하기](http://en.proft.me/2015/01/29/admin-actions-django-custom-form/)

   - ##### [내가 수정한 코드 일부](https://bitbucket.org/danodano/danoshop_server/commits/682e86556be9baf9cd59e1a5bdf1c5682d973734)

3. #### Admin.py

   ```python
       def export_selected_obj_to_delivery(self, request, queryset):
           # 선택된 품목을 출고파일로 내보내기

           if 'do_action' in request.POST:
             # 이하 생략....
           else:
               ids = queryset.values_list('id', flat=True)
               ids = list(set(ids))

               context = self.admin_site.each_context(request)
               context['opts'] = self.model._meta
               context['form'] = ExportDeliverySheetForm()
               context['title'] = u'출고파일 생성'
               context['export_items'] = ids

               return render(
                   request,
                   'export_delivery_sheet.html',
                   context,
               )

       export_selected_obj_to_delivery.short_description = u'선택된 품목을 출고파일로 내보내기'
   ```

4. ####Template(export_delivery_sheet.html) 

   ```html
   <!-- 이하 생략  -->
   <input type="hidden" name="action" value="export_selected_obj_to_delivery">
   <input type="hidden" name="do_action" value="yes">
   <!-- 이하 생략  -->

   {% for id in export_items %}
   	<input type="hidden" value="{{ id }}" name="order_item_row_id" />                   	<input type="hidden" name="_selected_action" value="{{ id }}">
   {% endfor %}
   ```

   ​


### 
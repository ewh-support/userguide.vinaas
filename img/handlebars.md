### Array

```
{{#each array_1}}
    {{this}}
{{/each}}
```

### **content**

```
{{{contents}}}
```

### Truy xuất đối tượng

#### Menu

```
{{#each AllCategory.children}}
	{{displayName}}
{{/each}}
```

### Hiển thị tên tác giả dưới mỗi bài viết trong category

> Ví dụ http://dulichmy.com/ghi-chep-duong-xa/

- Sử dụng `find` cho ngắn

```
{{#with (find ../AllCategory.files tacGiaId )}}{{title}}{{/with}}
```

### Hiển thị tên danh mục dưới mỗi bài viết trong category

> Ví dụ http://dulichmy.com/ghi-chep-duong-xa/

```
{{#with (lookupCategory ../AllCategory category)}}
    {{shortName displayName}}
{{/with}}
```

#### Cách 1

```
 {{#with (lookupCategory AllCategory pagination.category}} //.category
        {{#each children}}  //.category
            {{displayName}}
			{{children}}
            {{files}}
			{{href}}
        {{/each}}

        {{#each files}}  //loop  .md
        {{title}}
        {{slug}}
        {{/each}}
    {{/with}}
```

#### Cách 2

```
{{#each (lookupCategory AllCategory pagination.category 'children')}}
    data.addRow(['{{shortName displayName}}', {{length this.files}}]);
{{/each}}
```

### MENU Truy vấn .category, .md

- Category `phongban` có 2 thuộc tính là - `children` : category con của `phongban`, `displayName`: tên của category children, `href` - `files`: .md của category `phongban`, `title`: tên của file .md, `slug`

- Cấu trúc

```
- phongban (.category)
	|_ kinhdoanh(.category)
		|_ phong-kinh-doanh-so1(.md)
		|_ phong-kinh-doanh-so2(.md)
		|_ .. (.md)
	|_ marketing(.category)
		|_ .. (.md)
	|_ nhansu(.category)
		|_ .. (.md)
```

- Querry

```
	//.category
	{{#each (lookupCategory AllCategory 'phongban' 'children')}}
		<li>{{displayName}} </li>
	{{/each}}

	//.md
	{{#each (lookupCategory AllCategory 'phongban' 'files')}}
	  <li>{{title}}</li>
	{{/each}}
```

### this

- [{title}, {title}]

```
{{#each }}
    {{title}}
    {{this.title}}
{{/each}}
```

- ["aaa", "bbb"]

```
{{#each}}
    {{this}}
{{/each}}
```

### Hàm rút gọn

- `handlebars-helper.js`

```
 let findBySlug = function (AllCategoryFiles, slug) {
        //  console.log('findBySlug', slug, ', itemCount', AllCategoryFiles.length);
          try{
              var found = '';
              AllCategoryFiles.forEach(element => {
                  //console.log('element.slug ', element.slug)
                  if (slug === element.slug){
                    //  console.log('found', element);
                      found =  element;
                  }
              })
              return found;
          }catch(err){
              console.log(err)
              return '';
          }
      }

    Handlebars.registerHelper('find', findBySlug);
```

#### So sánh

- Rút gọn

```
{{#with (find ../AllCategory.files tangId )}}
    {{title}}
{{/with}}
```

- Cũ

```
{{#each (lookupCategory AllCategory 'batdongsan.can-ho' 'files')}}
	{{#each (lookupCategory ../AllCategory 'batdongsan.tang' 'files')}}
		{{#eq slug ../tangId}}
			batdongsan.tang.title {{title}} <br>
		{{/eq}}
	{{/each}}
{{/each}}
```

- Novaland

```
{{#each (lookupCategory AllCategory 'batdongsan.can-ho' 'files')}}
 1. tangId: {{tangId}} <br>
	{{#each (lookupCategory ../AllCategory 'batdongsan.tang' 'files')}}
		{{#eq slug ../tangId}}
			2. batdongsan.tang.title {{title}} <br>
				{{#each (lookupCategory ../../AllCategory 'batdongsan.block' 'files')}}
					{{#eq slug ../blockId}}
					3.blockId : {{title}} <br>
						{{#each (lookupCategory ../../../AllCategory 'batdongsan.chung-cu' 'files')}}
							{{#eq ../chungcuId slug}}
								4. chungcu : {{title}}
							{{/eq}}
						{{/each}}
					{{/eq}}
				{{/each}}

		{{/eq}}
	{{/each}}
{{/each}}
<hr>
su dung find:
{{#with (find ../AllCategory.files tangId)}}
	{{title}}
{{/with}}
```

### Câu lệnh thường sử dụng

> cấu trúc bắt đầu băng `{{# }}` và kết thúc bằng `{{/ }}`

- `eq`: Nếu `category` là `'batdongsan.can-ho.ban-giao'`

```
{{#eq category 'batdongsan.can-ho.ban-giao'}}
	<button type="button" class="btn m-btn--square btn-danger m-btn--wide" data-toggle="modal" data-target="#{{slug}}" >
		{{shortName title}}
	</button>
{{/eq}}
```

- `if` : Câu điều kiện if

```js
{{#if thuoc-tinh }}
    ...
{{else}}
    ...
{{/if}}
```

- `with` : Câu lệnh lấy từng thuộc tính (fields) của 1 đối tượng (object)

```js
{{#with object }}
   {{field-of-object}}
   {{this}}
{{/with}}
```

- `each`: Câu lệnh lặp từng phần tử trong danh sách
  - `@key` chỉ thứ tự mỗi phần tử trong danh sách, từ 0 tới phần tử cuối cùng
  - `@first` phần tử đầu tiên
  - `@last` phần tử cuối cùng trong danh sách

```js
{{#each danh-sach }}
   {{@key}}
   {{#if @first}}
      ...
   {{/if}}
{{/each }}
```

- `first` , `last` : lấy danh sách phần tử đầu, hoặc cuối cùng của danh sách, thường kết hợp với `each`

  - lặp 2 phần tử đầu tiên trong danh sách

  ```js
  {{#each (first danh-sach 2) }}
      ...
  {{/each}}
  ```

- `lt` : bé hơn, `gt`: lớn hơn, `is`: giống nhau
  - kiểm tra giá trị bé hơn 2,
  ```js
    {{#lt gia-tri-so 2}}
    {{/lt}}
  ```
- `moment` hiển thị thời gian theo cấu trúc, ngôn ngữ

  - `lang`: ngôn ngữ, `vi` việt nam, `en` tiếng anh
  - `format=" "` : cấu trúc hiển thị
  - ví dụ: hiển thị format theo tiếng việt, `thứ ba, 27 06 2017 22:01`

  ```js
     {{moment date lang="vi" format="dddd, DD MM YYYY HH:mm"}}
  ```

- `../` để lấy biến bên ngoài 1 cấp, ví dụ vòng lặp (`each`), hoặc (`with`)
  - ví dụ
  ```js
  {{AllCategory.children}}   //Notes:  truy cap AllCategory, lấy danh sách children
  {{#each ...}}
      {{../AllCategory.children}}  //Notes: dùng 1 lần  `../` để truy cập AllCategory
      {{#with ...}}
          {{../../AllCategory.children}}  //Notes: dùng 2 lần `../`  để truy cập AllCategory
      {{/each}}
  {{/each}}
  ```

### Biến toàn cục, cho phép truy cập trong mọi trang

- `AllCategory` : chứa Danh Mục root trên cùng chưa toàn bộ Danh Mục bên trong
- `AllTags`: chứa danh sách Chủ đề đang có của website

* `(lookupCategory AllCategory param-1 param-2)` : tìm Danh mục của website
  - `param-1`: tên của Danh mục cần tìm, ví dụ `thoi-su`, hoặc thuộc tính `category` nếu ở
  - `param-2` (tuỳ chọn): tên của thuộc tính của danh mục,
    - `children` : danh sách Danh Mục con
    - `files` : danh sách trang thuộc danh mục này
    - `href` : đường dẫn của trang Danh Mục này
    - `displayName` tên hiển thị của danh mục này

### Một số trường hợp thông dụng

- ví dụ: kiểm tra và hiển thị hình ảnh

```js
{{#if anh-dai-dien}} //Notes: nếu ảnh đại diện (anh-dai-dien) được nhập vào, thì hiển thị ra
    <img src="{{anh-dai-dien}}" alt="{{ghi-chu-anh-dai-dien}}"/>
{{else}}  //Notes: nếu không nhập, hiển thị hình mặc định
    <img src="https://easywebhub.com//img/no-available-image.png" alt="no image available"/>
{{/if}}
```

#### Hiển thị tên Danh Mục của 1 trang chi tiết

- Lấy tên đầy đủ `displayName` của Danh Mục của 1 trang
  - `href` : đường dẫn của Danh Mục
  - `displayName`: tên đầy đủ của Danh Mục

```js
{{#with (lookupCategory AllCategory category)}}
    <a class="tip" href="/{{href}}">{{displayName}}</a>
{{/with}}
```

#### Hiển thị các bài viết mới nhất trong Trang Chủ

- Lấy 6 bài viết mới nhất của website
- 2 bài đầu tiên hiển thị kiểu nổi bật
- 4 bài còn lại hiển thị ít nổi bật hơn

```js
{{#each (first AllCategory.files 6)}}
{{#lt @key 2}}
    <div class="block-top block-top-4">
      //Notes: hiển thị hai bài viết đầu tiên
    </div>
{{else}}
    <div class="block-top block-top-3">
       //Notes:  hiển thị 4 bài viết tiếp theo
    </div>
{{/lt}}
{{/each}}
```

#### Danh sách Danh Mục đang có của website

- hiển thị Danh Mục cấp 1

```js
<ul>
{{#each AllCategory.children }}
   <li><a href="/{{href}}" title="{{displayName}}">{{displayName}}</a></li>
{{/each}}
</ul>
```

- hiển thị Danh Mục cấp 1 và cấp 2

```js
{{#each AllCategory.children }}
   <li><a href="/{{href}}" title="{{displayName}}">{{displayName}}</a></li>
   <ul>
      {{#each this.children}} //Notes: lặp danh sách danh mục con cấp 2
          <li><a href="/{{href}}" title="{{displayName}}">{{displayName}}</a></li>
      {{/each}}
   </ul>
{{/each}}
```

#### Sắp xếp danh sách theo thứ tự định sẵn

> thường áp dụng cho menu

- Danh Mục trên menu cần sắp xếp theo thứ tự cho sẵn
- Cách 1: Sử dụng 1 array có thứ tự định trước
  - ví dụ `menu-order` , đặt trong meta/global để truy cập khắp website
  ```js
  "menu-order": ["thoi-su","kinh-doanh",  "van-hoa-giai-tri", "suc-khoe", "hitech"]
  ```
  - lặp theo array `menu-order`

```js
{{#each global.menu-order}}  //Notes: lặp dựa trên danh sách đã sắp thứ tự
    {{#each ../AllCategory.children }}  //Notes: lặp tất cả Danh Mục cấp 1
        {{#is this.category ../this}}
          //Notes: nếu giống nhau thì hiển thị , ../this là phần tử thuộc menu-order
         <li><a href="/{{href}}" title="{{displayName}}">{{displayName}}</a></li>
        {{/is}}
    {{/each}}
{{/each}}
    - Link demo : http://trang-tin-tuc.leanhdudu.easywebhub.me
```

- Cách 2: thêm 1 thuộc tính `order` trong từng Danh Mục, nằm trong `meta:`
  - sắp xếp dựa theo thuộc tính này.

#### 1. default category

```
<h1>EasyWeb</h1>
<h3>Default Category Layout</h3>

<h3>Mot so thuoc tinh cua Category</h3>

<p> Href:   {{href}} </p>
<p> category:   {{category}} </p>
<p> So luong bai viet:   {{files.length}} </p>
<p> layout:   {{layout}} </p>
<p> DisplayName:   {{displayName}} </p>

<h3>Tat ca tin tuc thuoc AllCategory</h3>
{{#each AllCategory.files}}
<h4> Bai viet so {{@key}} </h4>
<div>
      {{slug}} <br/>
      Danh Muc: {{category}} <br/>
      {{title}} <br/>
</div>
{{/each}}

<h3>tin tuc Danh Muc hien tai: {{pagination.displayName}}</h3>
{{#each pagination.files}}
<h4> Bai viet so {{@key}} </h4>
<div>
      {{slug}} <br/>
      Danh Muc: {{category}} <br/>
      {{title}} <br/>
</div>
{{/each}}
```

#### Show list of pages on category layout

- replace the fixed list on html code by query code to show list pages of current category
- using syntax bellow

```js
<h5>List files of {{pagination.category}} - {{pagination.categoryDisplayName}}<h5>
<ul>
{{#each pagination.files}}
	<li><a href="/{{ this.path }}">{{ this.title }}</a></li>
{{/each}}
</ul>

{{#if pagination.previous}}
	<a href="/{{pagination.previous.path}}">Previous Page </a>
{{/if}}
{{#if pagination.next}}
	<a href="/{{pagination.next.path}}">Next page</a>
{{/if}}
```

#### Preview Category pages

- show all category that no have parent category, call `level-1 category` at home page for testing

```js
<h5> All level-1 category of your website </h5>
<ul>
{{#each AllCategory.children}}
	<li><a href="/{{ this.path }}">{{ this.title }}</a></li>
{{/each}}
</ul>
```

- go to link to preview a category page
  - A category name, e.g `parent-name.name` will generated at path, `/parent-name/name`

#### Preview Category pages

- show all category that no have parent category, call `level-1 category` at home page for testing

```js
<h5> All level-1 category of your website </h5>
<ul>
{{#each AllCategory.children}}
	<li><a href="/{{ this.path }}">{{ this.title }}</a></li>
{{/each}}
</ul>
```

- go to link to preview a category page
  - A category name, e.g `parent-name.name` will generated at path, `/parent-name/name`

#### Connect Models

Nhân viên thuộc Phòng Ban, với Quyền Hạn
Ví dụ: tạo đối tượng Thực hiện Thẩm mĩ cho 1 Khách Hàng trên 1 Gói Dịch Vụ
tạo khachhangId, dichvuId, id ở đây là slug : duy nhất trong Prototype
liên kết 1 cấp

{{#with (find ../AllCategory.files khachhangId )}}{{title}}{{/with}}

liên kết nhiều cập

"tenDichVu" : "
{{#with (find ../AllCategory.files dichvuId )}}
{{#with (lookupCategory ../../AllCategory category)}}
{{displayName}}
{{/with}}
{{/with}}"

Đối tượng: thiết lập bởi Danh Mục cấp 1

khachhang, nhanvien, order, dich-vu, ....

phân chia đối tượng vào Danh Mục (category)

phân loại theo cây danh mục, ví dụ: cấu trúc Dịch Vu -> Gói Dịch Vụ
phân loại vào trạng thái, ví dụ: Đơn Hàng (order)

```
//emenu.html
{{#with (lookupCategory AllCategory 'order')}}
{{this.displayName}} | {{displayName}}
{{#each this.children}}
- danh mục con của danh mục Order
{{/each}}
{{/with}}




// filter của order
{{#each (lookupCategory AllCategory 'order' 'children')}}

        {{displayName}}

{{/each}}


format dữ liệu trước khi hiển thị
"date": "{{moment date format="DD-MM-YYYY HH:mm"}}"

{
    field: "Status",
    title: "Status",
    // callback function support for column rendering
    template: function (row) {
        var status = {
            1: {'title': 'Pending', 'class': 'm-badge--brand'},
            2: {'title': 'Delivered', 'class': ' m-badge--metal'},
            3: {'title': 'Canceled', 'class': ' m-badge--primary'},
            4: {'title': 'Success', 'class': ' m-badge--success'},
            5: {'title': 'Info', 'class': ' m-badge--info'},
            6: {'title': 'Danger', 'class': ' m-badge--danger'},
            7: {'title': 'Warning', 'class': ' m-badge--warning'}
        };
        return '' + status[row.Status].title + '';
    }


## Find ../slug tangId
```

{{#each (lookupCategory AllCategory 'batdongsan.can-ho' 'files') }}
{{#eq ../slug tangId }}
{{#eq category 'batdongsan.can-ho.hoan-thien'}}
<button type="button" class="btn m-btn--square btn-success m-btn--wide" data-toggle="modal" data-target="#{{slug}}" >
{{shortName title}}
</button>
{{/eq}}

{{#eq category 'batdongsan.can-ho.dang-xay'}}
<button type="button" class="btn m-btn--square btn-warning m-btn--wide" data-toggle="modal" data-target="#{{slug}}" >
{{shortName title}}
</button>
{{/eq}}

        {{#eq category 'batdongsan.can-ho.ban-giao'}}
        <button type="button" class="btn m-btn--square btn-default m-btn--wide" data-toggle="modal" data-target="#{{slug}}" >
            {{shortName title}}
        </button>
        {{/eq}}
    {{/eq}}
    {{/each}}

```
## Find tên tác giả
```

<div>
<a href="http://metrotam.com/author/vodacdanh/"
    title="Posts by Võ Đắc Danh" rel="author">
    {{#with (find ../AllCategory.files tacGiaId )}}
    {{title}}
    {{/with}}
    </a>
    <a href="{{pagination.href}}" rel="tag">
    {{#with (lookupCategory ../AllCategory category)}}
        {{shortName displayName}}
    {{/with}}
    </a>
</div>
```

# Tiêu điểm
```
{{#each (AllCategory.files)}}
    {{#eq tieuDiem 2}}
        <p>Đây là tiêu điểm 2</p>
    {{/eq}}
{{/each}}
```

## @key và @index
- @key : key value của object
- @index {{}}


/** 
 * Lấy limit danh sách bài viết có props = value
 * Áp dụng cho lấy danh sách bài viết có tinNoiBat: true hoặc có danh mục "tin-tuc-su-kien.tin-noi-bat"
 * "category": "tin-tuc-su-kien.tin-noi-bat-123"
 * 
 * Cú pháp: 
 * {{#each (getLimitBaseProps AllCategory.files "tin-tuc-su-kien.tin-noi-bat" "tinNoiBat" true 5)}}
 */
let getLimitBaseProps = function(allArticles, categoryCode, props, value, limit) {
	// console.log('limit', limit);
```	let list = [];
	try {
		for (let i = 0; i < allArticles.length; i++) {
			// console.log('props value', props, value) 
			// console.log('allArticles[i].props', allArticles[i][props]);
			if (allArticles[i]["category"] === categoryCode || (allArticles[i][props] && allArticles[i][props] === value)) {
				if (list.length < limit) {
					list.push(allArticles[i]);
				}
			}
		}


	} catch (error) {
		console.log('error', error);
	}
	// console.log('list', list.length);
	return list;
};
```

```
Handlebars.registerHelper("getLimitBaseProps", getLimitBaseProps);

let variableIsUrl = function(variable) {
	if (variable && variable.indexOf('/') === 0) {
		// trả về false nếu biến {{upload}} dạng '/img/...'
		return false;
	} else {
		// trả về true nếu biến {{upload}} dạng 'cdn.daknong.gov.vn/snv/....'
		return true;
	}
};
Handlebars.registerHelper("variableIsUrl", variableIsUrl);
```
# ap dung
```
{{#if upLoad}} 
    {{#if (variableIsUrl "upLoad")}}
        <p>Xem chi tiết <a href="{{upLoad}}">tại đây.</a></p>
    {{else}}
        <p>Xem chi tiết <a href="/{{upLoad}}">tại đây</a></p>
    {{/if}}
{{/if}}
```
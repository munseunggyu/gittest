## 6. 트러블 슈팅

### 1. 하위 컴포넌트의 요소를 가져오지 못 하는 이슈

-   문제 상황
    -   상위 컴포넌트에서 하위 컴포넌트의 요소를 선택해서 클릭 이벤트 또는 textContent를 가져와야 하는 상황에서 원하는 요소만 가져올 수 없다.
-   원인 추론
    -   클래스로 컴포넌트화하여 해당 컴포넌트를 render(or initialize) 하여야 요소들이 만들어지고 appendChild 하여야 해당 index.html에 그려져 querySelector로 가져올 수 없다.
-   해결 방법
    -   render 할 때 배열 형태로 보내주고 싶은 요소를 같이 보내준 후 해당 컴포넌트를 부를 때 구조 분해 할당으로 가져와 준다.
-   적용 코드)

    ```js
    // ./src/components/PostUploadForm/postUploadPreview.js
    import Component from '../../core/Component.js';

    class PostUploadPreview extends Component {
        constructor(props) {
            super(props);
        }

        render() {
            const previewImgCon = document.createElement('div');
            previewImgCon.setAttribute('class', 'post_img_con');

            const previewImg = document.createElement('img');
            previewImg.setAttribute('src', this.props);
            previewImg.setAttribute('class', 'post_img_preview');

            const imgCancelBtn = document.createElement('button');
            imgCancelBtn.setAttribute('class', 'post_btn_img_cancel');
            imgCancelBtn.textContent = 'x';

            previewImgCon.appendChild(previewImg);
            previewImgCon.appendChild(imgCancelBtn);

            return [previewImgCon, imgCancelBtn]; // 배열의 형태로 보내준다.
        }
    }
    export default PostUploadPreview;
    ```

    ```js
    // ./src/components/PostUploadForm/postUploadForm.js
    const [postUploadPreviewEl, imgCancelBtn] = postUploadPreview.initialize();
    ```

### 2. state로 상태 관리하여 리랜더링 시 정적인 데이터 초기화되는 이슈

-   문제상황
    -   state를 이용한 상태 관리에서 정적인 데이터를 지정하는 경우, 이벤트 발생으로 리렌더링을 진행하는 경우 정적인 데이터로 초기화된다.
-   원인 추론
    -   그 리렌더링 될 때 constructor까지 새로 실행되면서 정적인 값으로 변경이 되버린다.
-   해결 방법
    -   이벤트가 발생했을 때, 조건을 로컬 스토리지에 저장하여 해당 값을 이용해 상태 관리 변수의 값을 동적으로 할당하는 방식으로 오류를 해결했다.
-   적용 코드)

    ```js
    // ./src/javascript/components/mainPost/mainPst.js
    class MainPost extends Component {
        constructor(props) {
            super(props);
            this.categoryList = ['자유', '학습', '취업', '연애', '관계'];
            this.state = {
                displayPost: this.checkCategory(),
            };
        }
        checkCategory() {
            if (!localStorage.getItem('selectCategory')) {
                localStorage.setItem('selectCategory', '전체');
            }
            if (!localStorage.getItem('postOrder')) {
                localStorage.setItem('postOrder', '인기');
            }

            const category = localStorage.getItem('selectCategory');
            const order = localStorage.getItem('postOrder');

            let newList = [];
            if (this.categoryList.includes(category)) {
                newList = this.props.posts.filter(
                    (el) => el.category === category
                );
            } else {
                localStorage.setItem('selectCategory', '전체');
                newList = this.props.posts;
            }
            return this.orderPost(newList, order);
        }
        changeCategory(value) {
            localStorage.setItem('selectCategory', value);
            const newList = this.checkCategory();
            this.setState({ displayPost: newList });
        }

        changeOrder(value) {
            localStorage.setItem('postOrder', value);
            const newList = this.checkCategory();
            this.setState({ displayPost: newList });
        }
        render(){
          ...
        }
    }
    ```

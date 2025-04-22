<template>
  <view class="container">
    <!-- 顶部搜索区域 -->
    <view class="search-area">
      <view class="search-box">
        <!-- 搜索引擎选择器 -->
        <view class="search-engine-selector" @click="toggleSearchEngines">
          <image :src="currentSearchEngine.icon" class="search-engine-icon"></image>
          <view class="search-engine-arrow">▼</view>
          
          <!-- 搜索引擎下拉菜单 -->
          <view class="search-engine-dropdown" v-if="showSearchEngines">
            <view 
              v-for="(engine, idx) in searchEngines" 
              :key="idx"
              class="search-engine-option"
              @click.stop="selectSearchEngine(engine)"
            >
              <image :src="engine.icon" class="search-engine-option-icon"></image>
              <text class="search-engine-option-name">{{ engine.name }}</text>
            </view>
          </view>
        </view>
        
        <input 
          class="search-input" 
          v-model="searchValue" 
          placeholder="搜索你感兴趣的内容" 
          @keypress.enter="search"
        />
      </view>
      <button class="search-button" @click="search">搜索</button>
    </view>

    <!-- 导航链接区域 -->
    <view class="nav-container">
      <view class="nav-title">
        <text>常用网站导航</text>
      </view>
      <view class="nav-links">
        <view
          v-for="(link, index) in navLinks"
          :key="index"
          class="nav-link"
          :class="{ 'custom-link': link.isCustom }"
        >
          <view class="nav-link-content" @click="goToUrl(link.url)">
            <image :src="link.icon" class="nav-icon"></image>
            <text class="nav-text">{{ link.title }}</text>
          </view>
          
          <!-- 删除按钮 -->
          <view class="delete-btn" @click="deleteNavLink(index)">×</view>
        </view>
        
        <!-- 添加自定义导航按钮 -->
        <view class="nav-link add-link" @click="showAddModal = true">
          <view class="add-icon">+</view>
          <text class="nav-text">自定义添加</text>
        </view>
      </view>
    </view>


    <!-- 添加自定义导航的弹窗 -->
    <view class="modal-overlay" v-if="showAddModal" @click="showAddModal = false">
      <view class="modal-content" @click.stop>
        <view class="modal-header">
          <text class="modal-title">添加自定义导航</text>
          <text class="modal-close" @click="showAddModal = false">×</text>
        </view>
        
        <view class="modal-body">
          <view class="form-item">
            <text class="form-label">网站名称</text>
            <input v-model="newLink.title" placeholder="请输入网站名称" class="form-input" />
          </view>
          
          <view class="form-item">
            <text class="form-label">网站地址</text>
            <input v-model="newLink.url" placeholder="请输入以http://或https://开头的网址" class="form-input" />
          </view>
          
          <view class="form-item">
            <text class="form-label">图标选择</text>
            <view class="icon-options">
              <!-- 默认图标选择 -->
              <view class="icon-selector">
                <view 
                  v-for="(icon, idx) in defaultIcons" 
                  :key="idx"
                  class="icon-option"
                  :class="{ 'selected': newLink.icon === icon }"
                  @click="newLink.icon = icon"
                >
                  <image :src="icon" class="icon-preview"></image>
                </view>
              </view>
              
              <!-- 上传自定义图标 -->
              <view class="upload-custom-icon">
                <button class="upload-icon-btn" @click="uploadCustomIcon">上传自定义图标</button>
                <view class="custom-icon-preview" v-if="newLink.icon && !defaultIcons.includes(newLink.icon)">
                  <image :src="newLink.icon" class="icon-preview"></image>
                  <text class="preview-text">预览</text>
                </view>
              </view>
            </view>
          </view>
          
          <button class="add-button" @click="addCustomLink">添加</button>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      searchValue: '',
      showAddModal: false,
      newLink: {
        title: '',
        url: '',
        icon: '/static/logo.png',
        isCustom: true
      },
      defaultIcons: [
        '/static/logo.png',
        '/static/39jiankan.png',
        '/static/bohe.png',
        '/static/biying.png',
        '/static/chufang.png',
        '/static/zhihu.png'
      ],
      navLinks: [
        {
          icon: '/static/douyin.ico',
          title: '抖音',
          url: 'https://www.douyin.com/',
          isCustom: false
        },
        {
          icon: '/static/tb.jpeg',
          title: '淘宝',
          url: 'https://www.taobao.com/',
          isCustom: false
        },
        {
          icon: '/static/jindon.ico',
          title: '京东',
          url: 'https://www.jd.com',
          isCustom: false
        },
        {
          icon: '/static/fujian.png',
          title: '女性工具',
          url: 'http://www.fjplus.cn/zhuye-gongnen/gongju/index.html',
          isCustom: false
        },
        {
          icon: '/static/souhu.ico',
          title: '搜狐健康',
          url: 'http://health.sohu.com/',
          isCustom: false
        },
        {
          icon: '/static/weibo.ico',
          title: '微博',
          url: 'https://weibo.com/',
          isCustom: false
        },
        {
          icon: '/static/mangguotv.ico',
          title: '芒果TV',
          url: 'https://www.mgtv.com/',
          isCustom: false
        },
        {
          icon: '/static/aiqy.ico',
          title: '爱奇艺',
          url: 'http://www.iqiyi.com',
          isCustom: false
        },
        {
          icon: '/static/youku.ico',
          title: '优酷',
          url: 'http://www.youku.com',
          isCustom: false
        },
        {
          icon: '/static/chufang.png',
          title: '下厨房',
          url: 'https://www.xiachufang.com',
          isCustom: false
        },
        {
          icon: '/static/vip.ico',
          title: '唯品会',
          url: 'https://www.vip.com/',
          isCustom: false
        },
        {
          icon: '/static/mmw.jpeg',
          title: '妈妈网',
          url: 'http://www.mama.cn/',
          isCustom: false
        },
        {
          icon: '/static/shishang.ico',
          title: 'PCLady',
          url: 'http://www.pclady.com.cn/',
          isCustom: false
        },
        {
          icon: '/static/ruili.jpeg',
          title: '瑞丽时尚',
          url: 'https://www.rayli.com.cn/channel/video/shishang',
          isCustom: false
        },
        {
          icon: '/static/mogu.jpeg',
          title: '蘑菇街',
          url: 'https://m.mogu.com/?ptp=32.v5mL0b.0.0.GPPXOpre',
          isCustom: false
        }
      ],
      // 搜索引擎相关数据
      currentSearchEngine: {
        name: '必应',
        icon: '/static/biying.png',
        searchUrl: 'https://www.bing.com/search?q='
      },
      showSearchEngines: false,
      searchEngines: [
        {
          name: '百度',
          icon: '/static/baidu.ico',
          searchUrl: 'https://www.baidu.com/s?wd='
        },
        {
          name: '必应',
          icon: '/static/biying.ico',
          searchUrl: 'https://www.bing.com/search?q='
        },
        {
          name: 'Google',
          icon: '/static/guge.jpeg',
          searchUrl: 'https://www.google.com/search?q='
        },
        {
          name: '360',
          icon: '/static/360.ico',
          searchUrl: 'https://www.so.com/s?q='
        },
        {
          name: '搜狗',
          icon: '/static/sougou.ico',
          searchUrl: 'https://www.sogou.com/web?query='
        }
      ]
    };
  },
  onLoad() {
    // 从本地存储加载自定义链接
    try {
      const customLinks = uni.getStorageSync('customNavLinks');
      if (customLinks) {
        this.navLinks = [...this.navLinks, ...JSON.parse(customLinks)];
      }
      
      // 加载上次使用的搜索引擎
      const lastSearchEngine = uni.getStorageSync('lastSearchEngine');
      if (lastSearchEngine) {
        this.currentSearchEngine = JSON.parse(lastSearchEngine);
      }
    } catch (e) {
      console.error('Failed to load data from storage:', e);
    }
  },
  methods: {
    // 切换搜索引擎显示状态
    toggleSearchEngines() {
      this.showSearchEngines = !this.showSearchEngines;
    },
    
    // 选择搜索引擎
    selectSearchEngine(engine) {
      this.currentSearchEngine = engine;
      this.showSearchEngines = false;
      
      // 保存到本地存储
      try {
        uni.setStorageSync('lastSearchEngine', JSON.stringify(this.currentSearchEngine));
      } catch (e) {
        console.error('Failed to save search engine:', e);
      }
    },
    
    search() {
      if (!this.searchValue.trim()) return;
      
      const searchKeyword = this.searchValue;
      const searchUrl = `${this.currentSearchEngine.searchUrl}${encodeURIComponent(searchKeyword)}`;
      
      // 网页环境
      if (typeof window !== 'undefined') {
        window.open(searchUrl, '_blank');
      } 
      // App环境
      else if (typeof plus !== 'undefined') {
        plus.runtime.openURL(searchUrl);
      }
    },
    
    goToUrl(url) {
      // 同样根据环境选择打开方式
      if (typeof window !== 'undefined') {
        window.open(url, '_blank');
      } else if (typeof plus !== 'undefined') {
        plus.runtime.openURL(url);
      }
    },
    
    // 上传自定义图标
    uploadCustomIcon() {
      uni.chooseImage({
        count: 1,
        sizeType: ['compressed'],
        sourceType: ['album', 'camera'],
        success: (res) => {
          const tempFilePath = res.tempFilePaths[0];
          
          // 在真实应用中，您可能需要将图片上传到服务器
          // 这里简单处理，直接使用临时路径
          this.newLink.icon = tempFilePath;
          
          uni.showToast({
            title: '图标已选择',
            icon: 'success'
          });
        }
      });
    },
    
    addCustomLink() {
      // 基本验证
      if (!this.newLink.title.trim()) {
        uni.showToast({
          title: '请输入网站名称',
          icon: 'none'
        });
        return;
      }
      
      if (!this.newLink.url.trim() || 
          (!this.newLink.url.startsWith('http://') && !this.newLink.url.startsWith('https://'))) {
        uni.showToast({
          title: '请输入有效的网址',
          icon: 'none'
        });
        return;
      }
      
      // 创建新链接副本并添加到列表
      const newCustomLink = {
        icon: this.newLink.icon,
        title: this.newLink.title,
        url: this.newLink.url,
        isCustom: true
      };
      
      this.navLinks.push(newCustomLink);
      
      // 保存到本地存储
      try {
        const customLinks = this.navLinks.filter(link => link.isCustom);
        uni.setStorageSync('customNavLinks', JSON.stringify(customLinks));
      } catch (e) {
        console.error('Failed to save custom links:', e);
      }
      
      // 重置表单并关闭弹窗
      this.newLink = {
        title: '',
        url: '',
        icon: '/static/logo.png',
        isCustom: true
      };
      
      this.showAddModal = false;
      
      uni.showToast({
        title: '添加成功',
        icon: 'success'
      });
    },
    
    // 删除导航链接
    deleteNavLink(index) {
      // 确认是否删除
      uni.showModal({
        title: '确认删除',
        content: '确定要删除这个网址吗？',
        success: (res) => {
          if (res.confirm) {
            // 删除链接
            this.navLinks.splice(index, 1);
            
            // 更新存储
            try {
              const customLinks = this.navLinks.filter(link => link.isCustom);
              uni.setStorageSync('customNavLinks', JSON.stringify(customLinks));
              
              uni.showToast({
                title: '删除成功',
                icon: 'success'
              });
            } catch (e) {
              console.error('Failed to save custom links:', e);
            }
          }
        }
      });
    }
  }
};
</script>

<style>
.container {
  padding: 20px;
  background-color: #f8f9fa;
  min-height: 100vh;
}

/* 搜索区域样式 */
.search-area {
  display: flex;
  align-items: center;
  margin-bottom: 30px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  border-radius: 8px;
  background: #fff;
  padding: 8px;
}

.search-box {
  display: flex;
  align-items: center;
  flex: 1;
  background-color: #f5f5f5;
  border-radius: 6px;
  padding: 0 10px;
  margin-right: 10px;
}

/* 搜索引擎选择器样式 */
.search-engine-selector {
  display: flex;
  align-items: center;
  padding: 0 8px;
  margin-right: 8px;
  border-right: 1px solid #ddd;
  position: relative;
  cursor: pointer;
  height: 40px;
}

.search-engine-icon {
  width: 20px;
  height: 20px;
  margin-right: 4px;
}

.search-engine-arrow {
  font-size: 12px;
  color: #999;
}

.search-engine-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  background: white;
  border-radius: 6px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  width: 120px;
  z-index: 100;
  padding: 8px 0;
}

.search-engine-option {
  display: flex;
  align-items: center;
  padding: 8px 12px;
  cursor: pointer;
}

.search-engine-option:hover {
  background-color: #f5f5f5;
}

.search-engine-option-icon {
  width: 16px;
  height: 16px;
  margin-right: 8px;
}

.search-engine-option-name {
  font-size: 14px;
  color: #333;
}

.search-icon {
  margin-right: 10px;
  font-size: 18px;
  color: #999;
}

.search-input {
  flex: 1;
  height: 40px;
  font-size: 16px;
  border: none;
  background: transparent;
}

.search-button {
  background-color: #3e99f5;
  color: white;
  border: none;
  border-radius: 6px;
  padding: 0 20px;
  height: 40px;
  line-height: 40px;
  font-size: 16px;
}

/* 导航区域样式 */
.nav-container {
  background-color: #fff;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
}

.nav-title {
  margin-bottom: 20px;
  border-left: 4px solid #3e99f5;
  padding-left: 10px;
}

.nav-title text {
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.nav-links {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}

.nav-link {
  width: 25%;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 25px;
  position: relative;
}

.nav-link-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  position: relative;
}

.nav-icon {
  width: 50px;
  height: 50px;
  border-radius: 10px;
  margin-bottom: 8px;
  background-color: #f5f5f5;
}

.nav-text {
  font-size: 14px;
  color: #333;
  text-align: center;
  width: 80%;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* 删除按钮样式 */
.delete-btn {
  position: absolute;
  top: -8px;
  right: 4vw;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: #ff4d4f;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 12px;
  cursor: pointer;
  opacity: 0;
  transition: opacity 0.3s;
}

.nav-link:hover .delete-btn {
  opacity: 1;
}

.add-icon {
  width: 50px;
  height: 50px;
  border-radius: 10px;
  background-color: #f5f5f5;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  color: #999;
  margin-bottom: 8px;
  border: 1px dashed #ddd;
}

.custom-link::after {
  content: "自定义";
  position: absolute;
  top: -5px;
  right: 20px;
  background-color: #ffaa00;
  color: white;
  font-size: 10px;
  padding: 2px 4px;
  border-radius: 4px;
}

/* 弹窗样式 */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

.modal-content {
  width: 80%;
  background-color: #fff;
  border-radius: 10px;
  overflow: hidden;
}

.modal-header {
  padding: 15px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #eee;
}

.modal-title {
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.modal-close {
  font-size: 22px;
  color: #999;
}

.modal-body {
  padding: 20px;
}

.form-item {
  margin-bottom: 20px;
}

.form-label {
  display: block;
  margin-bottom: 8px;
  color: #333;
  font-size: 16px;
}

.form-input {
  width: 100%;
  height: 40px;
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 0 10px;
  font-size: 14px;
}

.icon-options {
  margin-top: 10px;
}

.icon-selector {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-bottom: 15px;
}

.icon-option {
  width: 40px;
  height: 40px;
  border-radius: 6px;
  border: 1px solid #eee;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
}

.icon-option.selected {
  border: 2px solid #3e99f5;
}

.icon-preview {
  width: 30px;
  height: 30px;
}

/* 上传自定义图标样式 */
.upload-custom-icon {
  margin-top: 10px;
}

.upload-icon-btn {
  background-color: #f5f5f5;
  border: 1px dashed #ddd;
  color: #666;
  width: 100%;
  height: 40px;
  border-radius: 6px;
  font-size: 14px;
}

.custom-icon-preview {
  margin-top: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.preview-text {
  font-size: 12px;
  color: #999;
  margin-top: 4px;
}

.add-button {
  background-color: #3e99f5;
  color: white;
  width: 100%;
  height: 44px;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  margin-top: 10px;
}

/* 响应式布局 */
@media screen and (max-width: 480px) {
  .nav-link {
    width: 33.33%;
  }
  
  .search-engine-selector {
    padding: 0 5px;
  }
  
  .search-engine-icon {
    margin-right: 0;
  }
  
  .search-engine-arrow {
    display: none;
  }
}

@media screen and (max-width: 375px) {
  .nav-link {
    width: 50%;
  }
  
  .search-area {
    flex-direction: column;
  }
  
  .search-box {
    width: 100%;
    margin-right: 0;
    margin-bottom: 10px;
  }
  
  .search-button {
    width: 100%;
  }
  
  .search-engine-dropdown {
    left: -10px;
  }
}
</style>

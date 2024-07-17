7/7(Sun)
サイドバーやモーダルを表示したとき、それ以外の部分を灰色にし、灰色をクリックすると閉じられるようにした。

```ts
  const [isSidebarOpen, setIsSidebarOpen] = useState(false);
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [selectedMenu, setSelectedMenu] = useState<Menu | undefined>(undefined);

  const handleSideNavOpen = () => {
    setIsSidebarOpen(true);
  };

  const handleSideNavClose = () => {
    setIsSidebarOpen(false);
  };

  const handleMenuView = (menu: Menu) => {
    setSelectedMenu(menu);
    setIsModalOpen(true);
  };

  const closeModal = () => {
    setIsModalOpen(false);
  };

// ～中略～

      {isModalOpen && selectedMenu && (
        <div>
          <div className="fixed inset-0 flex items-center justify-center pointer-events-none z-30">
            <Modal menu={selectedMenu} onClose={closeModal} />
          </div>
          <div
            className="fixed inset-0 bg-gray-600 opacity-50 z-20"
            onClick={closeModal}
          />
        </div>
      )}
```

inset-0 で全ての方向を 0 にして固定。 fixed でその場所から動かなくする。
pointer-events-none で z-index が下の部分も onClick が発動するようにする。
https://haayaaa.hatenablog.com/entry/2019/03/07/204958
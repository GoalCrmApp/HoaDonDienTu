# HoaDonDienTu

# Tài Liệu API Hóa Đơn Điện Tử VinVoice

> **Phiên bản**: 1.0  
> **Base URL đăng nhập lấy token**: `https://api-vinvoice.viettel.vn`
> **Base url chạy api** : `https://api-vinvoice.viettel.vn/services/einvoiceapplication` #https://api-vinvoice.viettel.vn/services/einvoiceapplication/api/InvoiceAPI/InvoiceWS/createInvoice/0100109106-509 tạo hóa đơn
## Mục Lục

1. [Giới Thiệu](#giới-thiệu)
2. [Xác Thực (Authentication)](#xác-thực-authentication)
3. [API Endpoints](#api-endpoints)
   - [Tạo Hóa Đơn](#1-tạo-hóa-đơn)
   - [Tìm Kiếm Hóa Đơn](#2-tìm-kiếm-hóa-đơn)
   - [Hủy Hóa Đơn](#3-hủy-hóa-đơn)
   - [Tạo Hash (USB Token)](#4-tạo-hash-usb-token)
4. [Data Models](#data-models)
5. [Mã Lỗi](#mã-lỗi)

---

## Giới Thiệu

API Hóa đơn điện tử VinVoice hỗ trợ các nghiệp vụ:
- ✅ Tạo hóa đơn mới (GTGT, Bán hàng, Phiếu xuất kho)
- ✅ Thay thế hóa đơn
- ✅ Điều chỉnh hóa đơn (thông tin/tiền)
- ✅ Hủy hóa đơn
- ✅ Tìm kiếm hóa đơn theo Transaction UUID
- ✅ Ký hóa đơn bằng USB Token

### Loại Hóa Đơn Được Hỗ Trợ

| Loại | Mã | Mô Tả |
|------|-----|-------|
| **Hóa đơn GTGT** | `1` | Hóa đơn giá trị gia tăng |
| **Hóa đơn bán hàng** | `2` | Hóa đơn bán hàng cho cá nhân |
| **Phiếu xuất kho** | `6` | Phiếu xuất kho hàng hóa |

---

## Xác Thực (Authentication)

Tất cả API đều yêu cầu **Bearer Token** trong header.

### Header Format

```http
Authorization: Bearer {access_token}
Content-Type: application/json
```

### Lấy Access Token

```http
POST /auth/login
Content-Type: application/json

{
  "username": "your_username",
  "password": "your_password"
}
```

---

## API Endpoints

### 1. Tạo Hóa Đơn

Tạo hóa đơn mới hoặc hóa đơn điều chỉnh/thay thế.

#### **Endpoint**

```http
POST /InvoiceAPI/InvoiceWS/createInvoice/{supplierTaxCode}
```

#### **URL Parameters**

| Tham số | Kiểu | Bắt buộc | Mô tả |
|---------|------|----------|-------|
| `supplierTaxCode` | string | ✅ | Mã số thuế người bán (VD: `0100109106-503`) |

#### **Request Body**

<details>
<summary><b>1.1. Tạo Hóa Đơn GTGT Mới</b></summary>

```json
{
  "generalInvoiceInfo": {
    "templateCode": "1/0173",
    "invoiceSeries": "K24TJS",
    "currencyCode": "VND",
    "adjustmentType": "1",
    "paymentStatus": true,
    "transactionUuid": "550e8400-e29b-41d4-a716-446655440000"
  },
  "buyerInfo": {
    "buyerName": "Nguyen Van An",
    "buyerLegalName": "Cong Ty TNHH ABC",
    "buyerTaxCode": "0100109106-990",
    "buyerAddressLine": "Duong Le Trong Tan, Ha Dong",
    "buyerPhoneNumber": "0912345678",
    "buyerEmail": "abc@gmail.com",
    "buyerIdNo": "030081002099",
    "buyerIdType": "1",
    "buyerCode": "NO_CODE",
    "buyerBankName": "Ngan Hang TMCP XYZ",
    "buyerBankAccount": "000193651773658",
    "buyerNotGetInvoice": 1
  },
  "payments": [
    {
      "paymentMethodName": "TM/CK"
    }
  ],
  "itemInfo": [
    {
      "itemCode": "HH001",
      "itemName": "May tinh",
      "unitName": "Chiec",
      "itemNote": "Chi chu hang hoa",
      "unitPrice": 15000000,
      "quantity": 2,
      "itemTotalAmountWithoutTax": 30000000,
      "taxPercentage": 10,
      "taxAmount": 3000000,
      "itemTotalAmountWithTax": 33000000,
      "itemTotalAmountAfterDiscount": 0,
      "discount": 0,
      "discount2": 0,
      "itemDiscount": 0,
      "selection": 1
    }
  ],
  "taxBreakdowns": [
    {
      "taxPercentage": 10,
      "taxableAmount": 30000000,
      "taxAmount": 3000000
    }
  ],
  "metadata": [
    {
      "keyTag": "invoiceNote",
      "valueType": "text",
      "stringValue": "Thong tin bo sung chuoi"
    }
  ],
  "summarizeInfo": {
    "discountAmount": 0,
    "totalAmountWithoutTax": 30000000,
    "totalTaxAmount": 3000000,
    "totalAmountWithTax": 33000000,
    "totalAmountAfterDiscount": 30000000
  }
}
```

</details>

<details>
<summary><b>1.2. Thay Thế Hóa Đơn</b></summary>

**Loại điều chỉnh**: `adjustmentType = "3"`

```json
{
  "generalInvoiceInfo": {
    "invoiceType": "1",
    "templateCode": "1/0173",
    "invoiceSeries": "K24TJS",
    "currencyCode": "VND",
    "adjustmentType": "3",
    "paymentStatus": true,
    "transactionUuid": "550e8400-e29b-41d4-a716-446655440001",
    "adjustedNote": "Thay thế hóa đơn bị sai",
    "originalInvoiceId": "K24TJS1",
    "originalInvoiceIssueDate": "1736818200000",
    "originalTemplateCode": "1/0173",
    "additionalReferenceDesc": "VĂN BAN THOA THUAN",
    "additionalReferenceDate": 1736818200000
  },
  "buyerInfo": { /* ... */ },
  "payments": [ /* ... */ ],
  "itemInfo": [ /* ... */ ],
  "summarizeInfo": { /* ... */ }
}
```

**Lưu ý**: Phải có đầy đủ thông tin hàng hóa mới để thay thế.

</details>

<details>
<summary><b>1.3. Điều Chỉnh Thông Tin</b></summary>

**Loại điều chỉnh**: `adjustmentType = "5"`, `adjustmentInvoiceType = "2"`

```json
{
  "generalInvoiceInfo": {
    "invoiceType": "1",
    "templateCode": "1/0173",
    "invoiceSeries": "K24TJS",
    "currencyCode": "VND",
    "adjustmentType": "5",
    "adjustmentInvoiceType": "2",
    "paymentStatus": true,
    "transactionUuid": "550e8400-e29b-41d4-a716-446655440002",
    "adjustedNote": "Điều chỉnh thông tin hóa đơn do bi nham",
    "originalInvoiceId": "K24TJS1",
    "originalInvoiceIssueDate": "1736818200000",
    "originalTemplateCode": "1/0173",
    "additionalReferenceDesc": "VĂN BAN THOA THUAN",
    "additionalReferenceDate": 1605682860000
  },
  "buyerInfo": { /* Thông tin mới cần điều chỉnh */ },
  "payments": [ /* ... */ ],
  "metadata": [ /* ... */ ]
}
```

**Lưu ý**: Không cần truyền thông tin hàng hóa khi chỉ điều chỉnh thông tin.

</details>

<details>
<summary><b>1.4. Điều Chỉnh Tiền</b></summary>

**Loại điều chỉnh**: `adjustmentType = "5"`, `adjustmentInvoiceType = "1"`

```json
{
  "generalInvoiceInfo": {
    "invoiceType": "1",
    "templateCode": "1/0173",
    "invoiceSeries": "K24TJS",
    "currencyCode": "VND",
    "adjustmentType": "5",
    "adjustmentInvoiceType": "1",
    "paymentStatus": true,
    "transactionUuid": "550e8400-e29b-41d4-a716-446655440003",
    "adjustedNote": "Điều chỉnh tiền do cong nham",
    "originalInvoiceId": "K25TII20",
    "originalInvoiceIssueDate": "1736818200000",
    "originalTemplateCode": "1/0230",
    "additionalReferenceDesc": "VĂN BAN THOA THUAN",
    "additionalReferenceDate": 1605682860000
  },
  "buyerInfo": { /* ... */ },
  "payments": [ /* ... */ ],
  "itemInfo": [
    {
      "itemCode": "HH001",
      "itemName": "May tinh",
      "unitName": "Chiec",
      "unitPrice": 15000000,
      "quantity": 2,
      "itemTotalAmountWithoutTax": 30000000,
      "taxPercentage": 10,
      "taxAmount": 3000000,
      "itemTotalAmountWithTax": 33000000,
      "isIncreaseItem": true,
      "selection": 1
    }
  ],
  "summarizeInfo": {
    "discountAmount": 0,
    "totalAmountWithoutTax": 30000000,
    "totalTaxAmount": 3000000,
    "totalAmountWithTax": 33000000,
    "totalAmountAfterDiscount": 30000000
  }
}
```

**Lưu ý**: `isIncreaseItem = true` khi tăng tiền, `false` khi giảm tiền.

</details>

<details>
<summary><b>1.5. Tạo Phiếu Xuất Kho (PXK)</b></summary>

```json
{
  "generalInvoiceInfo": {
    "invoiceType": "6",
    "templateCode": "6/1103",
    "invoiceSeries": "K24NAF",
    "currencyCode": "VND",
    "adjustmentType": "1",
    "paymentStatus": true,
    "transactionUuid": "550e8400-e29b-41d4-a716-446655440004",
    "typeId": 1,
    "classifyId": 1
  },
  "buyerInfo": { /* ... */ },
  "payments": [ /* ... */ ],
  "itemInfo": [ /* ... */ ],
  "metadata": [
    {
      "keyTag": "economicContractNo",
      "valueType": "text",
      "stringValue": "Lệnh điều động nội bộ"
    },
    {
      "keyTag": "transformer",
      "valueType": "text",
      "stringValue": "Tên người vận chuyển"
    },
    {
      "keyTag": "vehicle",
      "valueType": "text",
      "stringValue": "Phương tiện vận chuyển"
    },
    {
      "keyTag": "contractNo",
      "valueType": "text",
      "stringValue": "Hợp đồng số (Hợp đồng vận chuyển)"
    },
    {
      "keyTag": "exporterName",
      "valueType": "text",
      "stringValue": "Họ và tên người xuất hàng"
    },
    {
      "keyTag": "exportAt",
      "valueType": "text",
      "stringValue": "Xuất tại kho"
    },
    {
      "keyTag": "importAt",
      "valueType": "text",
      "stringValue": "Nhập tại kho"
    },
    {
      "keyTag": "commandDes",
      "valueType": "text",
      "stringValue": "Về việc"
    },
    {
      "keyTag": "commandDate",
      "valueType": "text",
      "stringValue": "Ngày tháng năm lệnh điều động"
    },
    {
      "keyTag": "cua",
      "valueType": "text",
      "stringValue": "Của"
    }
  ],
  "summarizeInfo": {
    "discountAmount": 0,
    "totalAmountWithoutTax": 30000000,
    "totalAmountWithTax": 30000000,
    "totalAmountAfterDiscount": 30000000
  }
}
```

</details>

<details>
<summary><b>1.6. Hóa Đơn Bán Hàng</b></summary>

```json
{
  "generalInvoiceInfo": {
    "templateCode": "2/0022",
    "invoiceSeries": "C24TAA",
    "currencyCode": "VND",
    "adjustmentType": "1",
    "paymentStatus": true,
    "transactionUuid": "550e8400-e29b-41d4-a716-446655440005",
    "typeId": 1,
    "classifyId": 1,
    "adjustAmount20": "5"
  },
  "buyerInfo": { /* ... */ },
  "payments": [ /* ... */ ],
  "itemInfo": [
    {
      "itemCode": "HH001",
      "itemName": "May tinh",
      "unitName": "Chiec",
      "unitPrice": 15000000,
      "quantity": 2,
      "itemTotalAmountWithoutTax": 30000000,
      "itemTotalAmountWithTax": 30000000,
      "adjustRatio": "5",
      "selection": 1
    }
  ],
  "meterReading": [
    {
      "meterName": "CS1",
      "currentIndex": "10",
      "previousIndex": "1",
      "amount": "9",
      "factor": "1"
    }
  ],
  "fuelReading": [
    {
      "batch": "BATCH01",
      "idLog": "LOG10",
      "noteLog": "Note Log",
      "priceLog": 100000,
      "productCode": "P01",
      "productName": "HH 01",
      "pumpCode": "PUM01",
      "pumpName": "PUM 01",
      "qtyLog": 1,
      "thanhTienLog": 100000,
      "startDate": 167800000000,
      "endDate": 167800000000
    }
  ],
  "qrCodeInfo": {
    "remainScan": 15,
    "temposType": "LOAI VE",
    "endDateQrcode": 167800000000,
    "totalScan": 15,
    "startDateQrcode": 166800000000
  },
  "summarizeInfo": {
    "discountAmount": 0,
    "totalAmountWithoutTax": 30000000,
    "totalAmountWithTax": 30000000,
    "totalAmountAfterDiscount": 30000000
  }
}
```

</details>

#### **Response**

```json
{
  "errorCode": "200",
  "description": "SUCCESS",
  "data": {
    "transactionID": "123456",
    "reservationCode": "ABC123XYZ",
    "invoiceNo": "K24TJS1"
  }
}
```

---

### 2. Tìm Kiếm Hóa Đơn

Tìm kiếm hóa đơn theo `transactionUuid`.

#### **Endpoint**

```http
POST /InvoiceAPI/InvoiceWS/searchInvoiceByTransactionUuid
Content-Type: application/x-www-form-urlencoded
```

#### **Request Body** (Form Data)

```
supplierTaxCode=0100109106-503
transactionUuid=4849decf-02a4-435b-8949-3b89b10167df
```

#### **Response**

```json
{
  "errorCode": "200",
  "description": "SUCCESS",
  "data": {
    "invoiceNo": "K24TJS1",
    "templateCode": "1/0173",
    "invoiceSeries": "K24TJS",
    "status": "COMPLETED",
    "reservationCode": "ABC123XYZ",
    "buyerInfo": { /* ... */ },
    "itemInfo": [ /* ... */ ],
    "summarizeInfo": { /* ... */ }
  }
}
```

---

### 3. Hủy Hóa Đơn

Hủy hóa đơn đã phát hành.

#### **Endpoint**

```http
POST /InvoiceAPI/InvoiceWS/cancelTransactionInvoice
Content-Type: application/x-www-form-urlencoded
```

#### **Request Body** (Form Data)

```
supplierTaxCode=0100109106-503
invoiceNo=K25TII20
templateCode=1/0230
strIssueDate=1736818200000
additionalReferenceDesc=TEN VAN BAN THOA THUAN
additionalReferenceDate=1587797116000
reasonDelete=Ly do xoa bo
```

#### **Parameters**

| Tham số | Kiểu | Bắt buộc | Mô tả |
|---------|------|----------|-------|
| `supplierTaxCode` | string | ✅ | Mã số thuế người bán |
| `invoiceNo` | string | ✅ | Số hóa đơn cần hủy |
| `templateCode` | string | ✅ | Mẫu số hóa đơn |
| `strIssueDate` | long | ✅ | Ngày phát hành (timestamp milliseconds) |
| `additionalReferenceDesc` | string | ✅ | Tên văn bản thỏa thuận |
| `additionalReferenceDate` | long | ✅ | Ngày văn bản (timestamp milliseconds) |
| `reasonDelete` | string | ❌ | Lý do xóa bỏ |

#### **Response**

```json
{
  "errorCode": "200",
  "description": "SUCCESS",
  "data": "Invoice cancelled successfully"
}
```

---

### 4. Tạo Hash (USB Token)

Tạo hash để ký hóa đơn bằng USB Token.

#### **Endpoint**

```http
POST /InvoiceAPI/InvoiceWS/createInvoiceUsbTokenGetHash/{supplierTaxCode}
```

#### **Request Body**

Giống như API tạo hóa đơn thông thường.

#### **Response**

```json
{
  "errorCode": "200",
  "description": "SUCCESS",
  "data": {
    "hashString": "abc123def456...",
    "templateCode": "1/0173",
    "transactionId": "123456"
  }
}
```

---

## Data Models

### GeneralInvoiceInfo

Thông tin chung của hóa đơn.

| Trường | Kiểu | Bắt buộc | Mô tả | Giá trị |
|--------|------|----------|-------|---------|
| `invoiceType` | string | ❌ | Loại hóa đơn | `1`: GTGT, `2`: Bán hàng, `6`: PXK |
| `templateCode` | string | ✅ | Mẫu số hóa đơn | VD: `1/0173` |
| `invoiceSeries` | string | ✅ | Ký hiệu hóa đơn | VD: `K24TJS` (max 9 ký tự) |
| `invoiceIssuedDate` | long | ❌ | Ngày phát hành | Timestamp milliseconds |
| `currencyCode` | string | ✅ | Mã tiền tệ | `VND`, `USD` (3 ký tự viết hoa) |
| `adjustmentType` | string | ❌ | Loại điều chỉnh | `1`: Mới, `3`: Thay thế, `5`: Điều chỉnh, `7`: Giải trình |
| `adjustmentInvoiceType` | string | ❌ | Loại hóa đơn điều chỉnh | `1`: Tiền, `2`: Thông tin |
| `originalInvoiceId` | string | ❌ | Số hóa đơn gốc | Khi điều chỉnh/thay thế |
| `originalInvoiceIssueDate` | string | ❌ | Ngày HĐ gốc | Format: `1736818200000` |
| `originalTemplateCode` | string | ❌ | Mẫu số HĐ gốc | Khi điều chỉnh/thay thế |
| `additionalReferenceDesc` | string | ❌ | Tên văn bản | Max 400 ký tự |
| `additionalReferenceDate` | long | ❌ | Ngày văn bản | Timestamp milliseconds |
| `paymentStatus` | boolean | ✅ | Trạng thái thanh toán | `true`: Đã TT, `false`: Chưa TT |
| `exchangeRate` | decimal | ❌ | Tỷ giá | Nếu tiền tệ khác VND |
| `transactionUuid` | string | ❌ | UUID giao dịch | 10-36 ký tự (unique) |
| `adjustedNote` | string | ❌ | Lý do điều chỉnh | Max 255 ký tự |
| `invoiceNote` | string | ❌ | Ghi chú hóa đơn | Max 4000 ký tự |
| `adjustAmount20` | string | ❌ | Tỷ lệ tính thuế | `0,1,2,3,5` |
| `typeId` | long | ❌ | ID loại vé | Dùng cho tem vé |
| `classifyId` | long | ❌ | ID phân loại vé | Dùng cho tem vé |
| `reservationCode` | string | ❌ | Mã đặt chỗ | Max 100 ký tự |

### BuyerInfo

Thông tin người mua.

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| `buyerName` | string | ❌ | Tên người mua | Max 800 ký tự |
| `buyerLegalName` | string | ❌ | Tên đơn vị | Max 1200 ký tự |
| `buyerTaxCode` | string | ❌ | Mã số thuế | Max 20 ký tự |
| `buyerAddressLine` | string | ❌ | Địa chỉ | Max 1200 ký tự |
| `buyerPhoneNumber` | string | ❌ | Số điện thoại | Max 35 ký tự, chỉ số |
| `buyerEmail` | string | ❌ | Email | Max 2000 ký tự, nhiều email cách nhau `;` |
| `buyerBankName` | string | ❌ | Tên ngân hàng | Max 200 ký tự |
| `buyerBankAccount` | string | ❌ | Số tài khoản | Max 100 ký tự |
| `buyerIdType` | string | ❌ | Loại giấy tờ | `1`: CMND, `2`: Hộ chiếu |
| `buyerIdNo` | string | ❌ | Số giấy tờ | Max 200 ký tự |
| `buyerCode` | string | ❌ | Mã khách hàng | Max 400 ký tự |
| `buyerNotGetInvoice` | int | ❌ | Không nhận HĐ | `0`: Có nhận, `1`: Không nhận |

### ItemInfo

Thông tin hàng hóa.

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| `selection` | int | ❌ | Loại hàng hóa | `1-6` |
| `itemCode` | string | ❌ | Mã hàng | |
| `itemName` | string | ❌ | Tên hàng | |
| `unitCode` | string | ❌ | Mã đơn vị | |
| `unitName` | string | ❌ | Tên đơn vị | |
| `unitPrice` | decimal | ❌ | Đơn giá | |
| `quantity` | decimal | ❌ | Số lượng | |
| `itemTotalAmountWithoutTax` | decimal | ❌ | Thành tiền chưa thuế | |
| `taxPercentage` | decimal | ❌ | % Thuế | `0`, `5`, `8`, `10` |
| `taxAmount` | decimal | ❌ | Tiền thuế | |
| `itemTotalAmountWithTax` | decimal | ❌ | Thành tiền có thuế | |
| `discount` | decimal | ❌ | Chiết khấu 1 | |
| `discount2` | decimal | ❌ | Chiết khấu 2 | |
| `itemDiscount` | decimal | ❌ | Tổng chiết khấu | |
| `itemTotalAmountAfterDiscount` | decimal | ❌ | Thành tiền sau CK | |
| `isIncreaseItem` | boolean | ❌ | Tăng/Giảm | `true`: Tăng, `false`: Giảm |
| `itemNote` | string | ❌ | Ghi chú | |
| `batchNo` | string | ❌ | Số lô | |
| `expDate` | string | ❌ | Hạn sử dụng | |
| `adjustRatio` | string | ❌ | Tỷ lệ điều chỉnh | `1,2,3,5` |

### PaymentInfo

Hình thức thanh toán.

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| `paymentMethodName` | string | ❌ | Tên hình thức TT | VD: `TM/CK`, `Tiền mặt` |
| `paymentMethod` | string | ❌ | Mã hình thức TT | `1-8` |

### SummarizeInfo

Tổng tiền hóa đơn.

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| `totalAmountWithoutTax` | decimal | ❌ | Tổng tiền chưa thuế | |
| `totalTaxAmount` | decimal | ❌ | Tổng tiền thuế | |
| `totalAmountWithTax` | decimal | ❌ | Tổng tiền có thuế | |
| `totalAmountWithTaxFrn` | decimal | ❌ | Tổng tiền ngoại tệ | |
| `totalAmountWithTaxInWords` | string | ❌ | Tổng tiền bằng chữ | |
| `discountAmount` | decimal | ❌ | Tổng chiết khấu | |
| `settlementDiscountAmount` | decimal | ❌ | CK thanh toán | |
| `totalAmountAfterDiscount` | decimal | ❌ | Tổng sau CK | |
| `totalAmountBeforeDiscount` | decimal | ❌ | Tổng trước CK | |

### MetaDataInfo

Thông tin bổ sung.

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| `keyTag` | string | ✅ | Khóa | Tên trường metadata |
| `valueType` | string | ✅ | Loại giá trị | `text`, `number`, `date` |
| `stringValue` | string | ❌ | Giá trị chuỗi | Max 13 ký tự |
| `numberValue` | long | ❌ | Giá trị số | Max 6 ký tự |
| `dateValue` | long | ❌ | Giá trị ngày | Timestamp |
| `keyLabel` | string | ❌ | Nhãn hiển thị | |
| `isRequired` | boolean | ❌ | Bắt buộc | |
| `isSeller` | boolean | ❌ | Thuộc người bán | |

### MeterReadingInfo

Thông tin điện nước (nếu có).

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `meterName` | string | Tên công tơ |
| `previousIndex` | string | Chỉ số cũ |
| `currentIndex` | string | Chỉ số mới |
| `factor` | string | Hệ số |
| `amount` | string | Sản lượng: `(current - previous) × factor` |

### FuelReadingInfo

Thông tin xăng dầu (nếu có).

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `idLog` | string | ID log |
| `pumpCode` | string | Mã trụ bơm |
| `pumpName` | string | Tên trụ bơm |
| `productCode` | string | Mã sản phẩm |
| `productName` | string | Tên sản phẩm |
| `qtyLog` | decimal | Số lượng |
| `priceLog` | decimal | Đơn giá |
| `thanhTienLog` | decimal | Thành tiền |
| `startDate` | long | Ngày bắt đầu |
| `endDate` | long | Ngày kết thúc |
| `batch` | string | Số lô |
| `noteLog` | string | Ghi chú |

### QrCodeInfo

Thông tin QR Code (tem vé).

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| `totalScan` | int | ✅ | Tổng số lần quét | 0-99 |
| `remainScan` | int | ✅ | Số lần quét còn lại | 0-99 |
| `startDateQrcode` | long | ✅ | Ngày bắt đầu | Timestamp |
| `endDateQrcode` | long | ✅ | Ngày kết thúc | Timestamp |
| `temposType` | string | ❌ | Loại vé | Max 200 ký tự |

---

## Mã Lỗi

| Mã lỗi | Mô tả |
|--------|-------|
| `200` | Thành công |
| `400` | Dữ liệu đầu vào không hợp lệ |
| `401` | Chưa xác thực hoặc token hết hạn |
| `403` | Không có quyền truy cập |
| `404` | Không tìm thấy hóa đơn |
| `500` | Lỗi hệ thống |

### Chi Tiết Lỗi Validation

| Mã lỗi | Trường | Mô tả |
|--------|--------|-------|
| `BAD_REQUEST_TAX_CODE_REQUIRED` | `supplierTaxCode` | Thiếu mã số thuế |
| `BAD_REQUEST_TEMPLATE_CODE_REQUIRED` | `templateCode` | Thiếu mẫu số hóa đơn |
| `BAD_REQUEST_INVOICE_SERIAL_REQUIRED` | `invoiceSeries` | Thiếu ký hiệu hóa đơn |
| `BAD_REQUEST_CURRENCY_CODE_REQUIRED` | `currencyCode` | Thiếu mã tiền tệ |
| `BAD_REQUEST_PAYMENT_STATUS_REQUIRED` | `paymentStatus` | Thiếu trạng thái thanh toán |
| `BAD_REQUEST_GENERAL_INVOICE_INFO_REQUIRED` | `generalInvoiceInfo` | Thiếu thông tin chung |
| `BAD_REQUEST_PAYMENT_INFO_REQUIRED` | `payments` | Thiếu hình thức thanh toán |
| `BAD_REQUEST_INVOICE_NO_INVALID` | `invoiceNo` | Số hóa đơn không hợp lệ (chỉ chứa a-z, A-Z, 0-9, /, -) |
| `BAD_REQUEST_CURRENCY_CODE_INVALID` | `currencyCode` | Mã tiền tệ không hợp lệ (phải viết hoa) |
| `BAD_REQUEST_ADJUSTMENT_TYPE_INVALID` | `adjustmentType` | Loại điều chỉnh không hợp lệ |

---

## Lưu Ý Quan Trọng

### 1. Transaction UUID
- Phải là **unique** cho mỗi hóa đơn
- Dùng để tra cứu hóa đơn sau khi tạo
- Format: UUID v4 (36 ký tự) hoặc chuỗi unique (10-36 ký tự)

### 2. Timestamp Format
- Tất cả các trường ngày tháng đều theo format **milliseconds** từ epoch
- VD: `1736818200000` = `2025-01-14 10:30:00`

### 3. Loại Điều Chỉnh
- `adjustmentType = "1"`: Hóa đơn mới
- `adjustmentType = "3"`: Thay thế hóa đơn
- `adjustmentType = "5"`: Điều chỉnh hóa đơn
  - `adjustmentInvoiceType = "1"`: Điều chỉnh tiền
  - `adjustmentInvoiceType = "2"`: Điều chỉnh thông tin
- `adjustmentType = "7"`: Giải trình

### 4. Thuế GTGT
- `0%`: Không chịu thuế
- `5%`: Thuế suất 5%
- `8%`: Thuế suất 8%
- `10%`: Thuế suất 10%

### 5. Selection (Loại Hàng Hóa)
- `1`: Hàng hóa, dịch vụ
- `2`: Khuyến mại
- `3`: Chiết khấu
- `4`: Ghi chú
- `5`: Tăng thêm
- `6`: Giảm trừ

---

## Code Examples

### TypeScript/Next.js

```typescript
// lib/vinvoice-api.ts
export interface InvoiceCreateRequest {
  generalInvoiceInfo: GeneralInvoiceInfo;
  buyerInfo: BuyerInfo;
  payments: PaymentInfo[];
  itemInfo: ItemInfo[];
  summarizeInfo: SummarizeInfo;
  metadata?: MetaDataInfo[];
}

export async function createInvoice(
  supplierTaxCode: string,
  data: InvoiceCreateRequest,
  accessToken: string
) {
  const response = await fetch(
    `https://api-vinvoice.viettel.vn/services/einvoiceapplication/api/InvoiceAPI/InvoiceWS/createInvoice/${supplierTaxCode}`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${accessToken}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data),
    }
  );

  if (!response.ok) {
    throw new Error(`API Error: ${response.status}`);
  }

  return response.json();
}

// Sử dụng
const invoice = {
  generalInvoiceInfo: {
    templateCode: "1/0173",
    invoiceSeries: "K24TJS",
    currencyCode: "VND",
    adjustmentType: "1",
    paymentStatus: true,
    transactionUuid: crypto.randomUUID(),
  },
  buyerInfo: {
    buyerName: "Nguyen Van A",
    buyerTaxCode: "0100109106",
    buyerEmail: "buyer@example.com",
  },
  payments: [{ paymentMethodName: "TM/CK" }],
  itemInfo: [
    {
      itemCode: "SP001",
      itemName: "San pham 1",
      unitName: "Cai",
      unitPrice: 100000,
      quantity: 2,
      itemTotalAmountWithoutTax: 200000,
      taxPercentage: 10,
      taxAmount: 20000,
      itemTotalAmountWithTax: 220000,
      selection: 1,
    },
  ],
  summarizeInfo: {
    totalAmountWithoutTax: 200000,
    totalTaxAmount: 20000,
    totalAmountWithTax: 220000,
  },
};

const result = await createInvoice("0100109106-503", invoice, "your_access_token");
console.log("Invoice created:", result.data.invoiceNo);
```

### Tìm Kiếm Hóa Đơn

```typescript
export async function searchInvoiceByUuid(
  supplierTaxCode: string,
  transactionUuid: string,
  accessToken: string
) {
  const formData = new URLSearchParams({
    supplierTaxCode,
    transactionUuid,
  });

  const response = await fetch(
    'https://vinvoice.viettel.vn/api/services/einvoiceapplication/api/InvoiceAPI/InvoiceWS/searchInvoiceByTransactionUuid',
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${accessToken}`,
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: formData,
    }
  );

  return response.json();
}
```

### Hủy Hóa Đơn

```typescript
export async function cancelInvoice(
  data: {
    supplierTaxCode: string;
    invoiceNo: string;
    templateCode: string;
    strIssueDate: number;
    additionalReferenceDesc: string;
    additionalReferenceDate: number;
    reasonDelete: string;
  },
  accessToken: string
) {
  const formData = new URLSearchParams(data as any);

  const response = await fetch(
    'https://api-vinvoice.viettel.vn/services/einvoiceapplication/api/InvoiceAPI/InvoiceWS/cancelTransactionInvoice',
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${accessToken}`,
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: formData,
    }
  );

  return response.json();
}
```

---

## FAQ

**Q: Làm sao để lấy access token?**  
A: Liên hệ với VinVoice để được cấp tài khoản và sử dụng API `/auth/login` để lấy token.

**Q: Transaction UUID có thể trùng không?**  
A: Không. Mỗi hóa đơn phải có một UUID unique. Nên dùng `crypto.randomUUID()` để tạo.

**Q: Có thể điều chỉnh hóa đơn nhiều lần không?**  
A: Có, nhưng mỗi lần điều chỉnh phải tạo một hóa đơn điều chỉnh mới tham chiếu đến hóa đơn gốc.

**Q: Hóa đơn đã hủy có thể phục hồi không?**  
A: Không. Hóa đơn đã hủy là vĩnh viễn. Nếu cần, phải tạo hóa đơn mới.

**Q: Thời gian xử lý API là bao lâu?**  
A: Thông thường 1-3 giây. Trong giờ cao điểm có thể lên đến 5-10 giây.

---

## Hỗ Trợ

- **Website**: https://vinvoice.viettel.vn
- **Email**: support@vinvoice.vn
- **Hotline**: 1800 8000

---

*Tài liệu được tạo dựa trên mã nguồn mẫu từ VinVoice. Cập nhật lần cuối: 2025-01-14*

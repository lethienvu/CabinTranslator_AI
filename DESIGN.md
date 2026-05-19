---
version: alpha
name: Cabin Translator
description: Glassmorphism td1i gia3n, tadp trung va0o nd9i dung phibfn dcbch thddi gian thf1c.
colors:
  primary: "#093FB4"
  secondary: "#B52509"
  primary-hover: "#2359CE"
  primary-active: "#002DA2"
  secondary-hover: "#CF3F23"
  secondary-active: "#A31300"
  background-primary: "#0F0F14"
  background-secondary: "#191923"
  background-glass: "#1E1E2D"
  background-input: "#282837"
  background-hover: "#3C3C50"
  background-active: "#50506E"
  border-subtle: "#0F0F0F"
  border-light: "#1A1A1A"
  border-focus: "#093FB4"
  text-primary: "#FFFFFF"
  text-secondary: "#8C8C8C"
  text-muted: "#595959"
  text-provisional: "#666666"
  success: "#4ADE80"
  warning: "#FBBF24"
  error: "#B52509"
  accent-glow: "#093FB4"
  accent-glow-strong: "#093FB4"
  error-glow: "#B52509"
  error-glow-strong: "#B52509"
typography:
  body:
    fontFamily: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Helvetica Neue', Arial, sans-serif
    fontSize: 14px
    fontWeight: 400
    lineHeight: 1.4
    letterSpacing: "0px"
  caption:
    fontFamily: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Helvetica Neue', Arial, sans-serif
    fontSize: 11px
    fontWeight: 500
    lineHeight: 1.2
    letterSpacing: "0.02em"
  label:
    fontFamily: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Helvetica Neue', Arial, sans-serif
    fontSize: 12px
    fontWeight: 600
    lineHeight: 1.2
    letterSpacing: "0.04em"
  button:
    fontFamily: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Helvetica Neue', Arial, sans-serif
    fontSize: 13px
    fontWeight: 600
    lineHeight: 1.0
    letterSpacing: "0px"
rounded:
  sm: 6px
  md: 10px
  lg: 14px
spacing:
  xs: 4px
  sm: 8px
  md: 12px
  lg: 16px
  xl: 24px
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "#FFFFFF"
    rounded: "{rounded.sm}"
    padding: 10px
  button-primary-hover:
    backgroundColor: "{colors.primary-hover}"
    textColor: "#FFFFFF"
    rounded: "{rounded.sm}"
  button-secondary:
    backgroundColor: "{colors.secondary}"
    textColor: "#FFFFFF"
    rounded: "{rounded.sm}"
    padding: 10px
  input-default:
    backgroundColor: "{colors.background-input}"
    textColor: "{colors.text-primary}"
    rounded: "{rounded.sm}"
---

## Overview

Thibft kbf tadp trung va0o nd9i dung phibfn dcbch thddi gian thf1c vdbi ldbp kdnh mdd td1i gia3n. Giao dic7n 0u tiadn d9 rbf ce7a vc3n ba3n va0 kha3 nbfng thao ta1c nhanh. Ba3ng ma0u mdbi b7t trcdng ta1m va0o xanh d1m (#093FB4) cho ha1nh d9ng chd5nh va0 d1 d1m (#B52509) la1m ma0u the9 ca5p cho tra1ng tha1i ld5i/ghi a3m.

## Colors

- Primary #093FB4: màu hành động chính, trạng thái active, các highlight chính.
- Secondary #B52509: trạng thái ghi âm, lỗi và cảnh báo nhấn mạnh.
- Nền tối trong suốt với glassmorphism để giữ tập trung vào nội dung.

## Typography

System font (-apple-system / Segoe UI) là font chính cho toàn bộ UI — không cần load mạng. Cỡ chữ nhỏ (11–14px) để phù hợp overlay gọn, với các nhãn và trạng thái cỡ letter-spacing nhẹ để dễ đọc.

## Layout

- Overlay gọn, tập trung ở trung tâm.
- Khoảng cách dựa trên bậc 4px/8px/12px/16px/24px.

## Elevation & Depth

- Glassmorphism nhb9: blur + border mdd.
- Shadow veba d1 d1 tebch ldbp khcfi nc1n.

## Shapes

- Bo gd1c nhcf (6b314px) d1 gief ca3m gia3c tinh gcdn.

## Components

- Button chd5nh dd1ng primary, hover theo primary-hover.
- Button phe5 va0 tra1ng tha1i ld5i dd1ng secondary.
- Input gief nc1n td1i trong sud1t d1 d3ng nha5t.

## Do's and Don'ts

- Do: dd1ng primary cho CTA chd5nh, secondary cho ld5i/ghi a3m.
- Donb9t: pha thb5m ma0u kha3c ngoa0i ba3ng ma0u.

# -"use client"

import { useState, useRef } from "react"
import { 
  Brain, 
  Thermometer, 
  Stethoscope, 
  Pill, 
  BarChart3,
  TrendingUp,
  Bell,
  MapPin,
  AlertTriangle,
  CheckCircle,
  Activity,
  Package,
  Truck,
  Shield
} from "lucide-react"
import { Navbar } from "@/components/navbar"
import { Footer } from "@/components/footer"
import { Button } from "@/components/ui/button"
import { cn } from "@/lib/utils"
import { useLanguage } from "@/lib/i18n"
import { ForecastChart, StockAnalysisChart, LowStockAlerts, UsageTrendChart } from "@/components/chart-replacements"
import {
  ColdChainPanel,
  DiagnosisAssistant,
  DrugInteractionChecker,
  AnalyticsDashboard,
} from "@/components/feature-components"

export default function FeaturesPage() {
  const { t } = useLanguage()
  const [activeFeature, setActiveFeature] = useState("forecasting")
  const [selectedCapability, setSelectedCapability] = useState<string | null>("forecastGraph")
  const [stockData, setStockData] = useState<Array<any>>([])
  const [stockLoading, setStockLoading] = useState(false)
  const [stockError, setStockError] = useState<string | null>(null)

  async function fetchStock() {
    setStockLoading(true)
    setStockError(null)
    try {
      const res = await fetch('/api/inventory')
      const payload = await res.json()
      if (!res.ok) {
        setStockError(payload?.error || 'Failed to fetch')
        setStockData([])
      } else {
        // payload.data expected to be array
        setStockData(payload.data || [])
      }
    } catch (err) {
      setStockError(String(err))
      setStockData([])
    } finally {
      setStockLoading(false)
    }
  }

  const features = [
    {
      id: "forecasting",
      icon: Brain,
      title: t.featuresPage.forecasting.title,
      subtitle: t.featuresPage.forecasting.subtitle,
      color: "teal",
      description: t.featuresPage.forecasting.description,
      capabilities: [
        { id: "forecastGraph", icon: TrendingUp, text: t.featuresPage.forecasting.cap1 },
        { id: "stockAnalysis", icon: Package, text: t.featuresPage.forecasting.cap2 },
        { id: "lowStockAlert", icon: Bell, text: t.featuresPage.forecasting.cap3 },
        { id: "usageTrends", icon: Activity, text: t.featuresPage.forecasting.cap4 },
      ],
    },
    {
      id: "coldchain",
      icon: Thermometer,
      title: t.featuresPage.coldchain.title,
      subtitle: t.featuresPage.coldchain.subtitle,
      color: "amber",
      description: t.featuresPage.coldchain.description,
      capabilities: [
        { id: "coldchainLive", icon: Thermometer, text: t.featuresPage.coldchain.cap1 },
        { id: "coldchainAnomaly", icon: AlertTriangle, text: t.featuresPage.coldchain.cap2 },
        { id: "coldchainLocation", icon: MapPin, text: t.featuresPage.coldchain.cap3 },
        { id: "coldchainCompliance", icon: Shield, text: t.featuresPage.coldchain.cap4 },
      ],
    },
    {
      id: "diagnosis",
      icon: Stethoscope,
      title: t.featuresPage.diagnosis.title,
      subtitle: t.featuresPage.diagnosis.subtitle,
      color: "teal",
      description: t.featuresPage.diagnosis.description,
      capabilities: [
        { icon: Stethoscope, text: t.featuresPage.diagnosis.cap1 },
        { icon: Brain, text: t.featuresPage.diagnosis.cap2 },
        { icon: Activity, text: t.featuresPage.diagnosis.cap3 },
        { icon: CheckCircle, text: t.featuresPage.diagnosis.cap4 },
      ],
    },
    {
      id: "interactions",
      icon: Pill,
      title: t.featuresPage.interactions.title,
      subtitle: t.featuresPage.interactions.subtitle,
      color: "amber",
      description: t.featuresPage.interactions.description,
      capabilities: [
        { icon: Pill, text: t.featuresPage.interactions.cap1 },
        { icon: AlertTriangle, text: t.featuresPage.interactions.cap2 },
        { icon: Shield, text: t.featuresPage.interactions.cap3 },
        { icon: CheckCircle, text: t.featuresPage.interactions.cap4 },
      ],
    },
    {
      id: "analytics",
      icon: BarChart3,
      title: t.featuresPage.analytics.title,
      subtitle: t.featuresPage.analytics.subtitle,
      color: "teal",
      description: t.featuresPage.analytics.description,
      capabilities: [
        { id: "kpiTracking", icon: BarChart3, text: t.featuresPage.analytics.cap1 },
        { id: "wasteAnalysis", icon: Package, text: t.featuresPage.analytics.cap2 },
        { id: "deliverySuccess", icon: Truck, text: t.featuresPage.analytics.cap3 },
        { id: "trendForecasting", icon: TrendingUp, text: t.featuresPage.analytics.cap4 },
      ],
    },
  ]

  const active = features.find(f => f.id === activeFeature)!

  const ActiveIcon = active.icon as any

  const [selectedDrugType, setSelectedDrugType] = useState("antibiotics")
  const [forecastRange, setForecastRange] = useState<"3" | "6" | "9">("6")
  const [seasonMultiplier, setSeasonMultiplier] = useState(1)
  const forecastRef = useRef<HTMLDivElement | null>(null)

  const drugTypes = [
    { id: "antibiotics", label: t.featuresPage.forecasting.drugTypes.antibiotics },
    { id: "painkillers", label: t.featuresPage.forecasting.drugTypes.painkillers },
    { id: "diabetes", label: t.featuresPage.forecasting.drugTypes.diabetes },
    { id: "coldCough", label: t.featuresPage.forecasting.drugTypes.coldCough },
    { id: "cardiovascular", label: t.featuresPage.forecasting.drugTypes.cardiovascular },
  ]

  const drugTypeInfo: Record<string, { peak: string; volatility: string; note: string }> = {
    antibiotics: {
      peak: "Peak season: Q2",
      volatility: "Moderate",
      note: "อัตราการใช้สูงขึ้นในช่วงรอบการติดเชื้อและการรักษาโรงพยาบาล",
    },
    painkillers: {
      peak: "Peak season: Year-round",
      volatility: "Low",
      note: "ความต้องการสม่ำเสมอในร้านยาทั่วประเทศ ขึ้นกับฤดูกาลและเทศกาล",
    },
    diabetes: {
      peak: "Peak season: Q1",
      volatility: "Stable",
      note: "การบริโภคยาควบคุมระดับน้ำตาลมีแนวโน้มเติบโตต่อเนื่อง",
    },
    coldCough: {
      peak: "Peak season: Winter",
      volatility: "High",
      note: "มีการเปลี่ยนแปลงตามสภาพอากาศและการระบาดของหวัด",
    },
    cardiovascular: {
      peak: "Peak season: Q3",
      volatility: "Moderate",
      note: "ตลาดยาหัวใจได้รับแรงหนุนจากผู้สูงอายุและการตรวจสุขภาพ",
    },
  }

  const forecastData: Record<string, Array<{ month: string; actual: number; predicted: number }>> = {
    antibiotics: [
      { month: "Jan", actual: 1250, predicted: 1320 },
      { month: "Feb", actual: 1180, predicted: 1240 },
      { month: "Mar", actual: 1340, predicted: 1400 },
      { month: "Apr", actual: 1290, predicted: 1350 },
      { month: "May", actual: 1420, predicted: 1480 },
      { month: "Jun", actual: 1500, predicted: 1570 },
      { month: "Jul", actual: 1550, predicted: 1620 },
      { month: "Aug", actual: 1490, predicted: 1575 },
      { month: "Sep", actual: 1520, predicted: 1600 },
    ],
    painkillers: [
      { month: "Jan", actual: 980, predicted: 1020 },
      { month: "Feb", actual: 920, predicted: 980 },
      { month: "Mar", actual: 1040, predicted: 1100 },
      { month: "Apr", actual: 1120, predicted: 1180 },
      { month: "May", actual: 1180, predicted: 1240 },
      { month: "Jun", actual: 1260, predicted: 1320 },
      { month: "Jul", actual: 1290, predicted: 1350 },
      { month: "Aug", actual: 1330, predicted: 1390 },
      { month: "Sep", actual: 1370, predicted: 1440 },
    ],
    diabetes: [
      { month: "Jan", actual: 720, predicted: 760 },
      { month: "Feb", actual: 740, predicted: 780 },
      { month: "Mar", actual: 780, predicted: 820 },
      { month: "Apr", actual: 810, predicted: 860 },
      { month: "May", actual: 860, predicted: 910 },
      { month: "Jun", actual: 900, predicted: 960 },
      { month: "Jul", actual: 930, predicted: 990 },
      { month: "Aug", actual: 960, predicted: 1020 },
      { month: "Sep", actual: 980, predicted: 1040 },
    ],
    coldCough: [
      { month: "Jan", actual: 860, predicted: 900 },
      { month: "Feb", actual: 940, predicted: 980 },
      { month: "Mar", actual: 1020, predicted: 1080 },
      { month: "Apr", actual: 1180, predicted: 1240 },
      { month: "May", actual: 1300, predicted: 1360 },
      { month: "Jun", actual: 1420, predicted: 1480 },
      { month: "Jul", actual: 1460, predicted: 1520 },
      { month: "Aug", actual: 1490, predicted: 1550 },
      { month: "Sep", actual: 1520, predicted: 1580 },
    ],
    cardiovascular: [
      { month: "Jan", actual: 680, predicted: 720 },
      { month: "Feb", actual: 710, predicted: 760 },
      { month: "Mar", actual: 750, predicted: 800 },
      { month: "Apr", actual: 790, predicted: 845 },
      { month: "May", actual: 830, predicted: 890 },
      { month: "Jun", actual: 870, predicted: 930 },
      { month: "Jul", actual: 900, predicted: 960 },
      { month: "Aug", actual: 930, predicted: 995 },
      { month: "Sep", actual: 960, predicted: 1020 },
    ],
  }

  const chartData = forecastData[selectedDrugType] || []
  const displayedChartData = chartData.slice(0, Number(forecastRange) || chartData.length)
  const maxValue = Math.max(1, ...displayedChartData.flatMap(d => [d.actual, d.predicted]))
  const forecastBreakIndex = displayedChartData.length > 3 ? displayedChartData.length - 3 : Math.max(1, displayedChartData.length - 1)

  const chartPoints = displayedChartData.map((d, index) => {
    const x = displayedChartData.length === 1
      ? 50
      : 10 + (index / (displayedChartData.length - 1)) * 80
    const yActual = 92 - (d.actual / maxValue) * 74
    const yPredicted = 92 - (d.predicted / maxValue) * 74
    return { ...d, x, yActual, yPredicted }
  })

  const actualPoints = chartPoints.slice(0, forecastBreakIndex)
  const predictedPoints = chartPoints.slice(Math.max(forecastBreakIndex - 1, 0))

  const buildCurvePath = (points: Array<any>, valueKey: 'yActual' | 'yPredicted') => {
    if (points.length === 0) return ""
    if (points.length === 1) return `M ${points[0].x} ${points[0][valueKey]}`
    return points.reduce((path, point, index) => {
      if (index === 0) {
        return `M ${point.x} ${point[valueKey]}`
      }
      const prev = points[index - 1]
      const cx = (prev.x + point.x) / 2
      const cy = (prev[valueKey] + point[valueKey]) / 2
      return `${path} Q ${prev.x} ${prev[valueKey]} ${cx} ${cy}`
    }, "")
  }

  const actualPath = buildCurvePath(actualPoints, 'yActual')
  const predictedPath = buildCurvePath(predictedPoints, 'yPredicted')
  const actualAreaPath = actualPoints.length > 1
    ? `${actualPath} L ${actualPoints[actualPoints.length - 1].x} 92 L ${actualPoints[0].x} 92 Z`
    : ''
  const predictedAreaPath = predictedPoints.length > 1
    ? `${predictedPath} L ${predictedPoints[predictedPoints.length - 1].x} 92 L ${predictedPoints[0].x} 92 Z`
    : ''

  return (
    <div className="min-h-screen bg-background">
      <Navbar />
      
      {/* Hero */}
      <section className="pt-32 pb-16 px-4 bg-gradient-to-b from-light-gray to-background">
        <div className="max-w-7xl mx-auto text-center">
          <h1 className="text-4xl md:text-5xl font-bold text-navy mb-4">
            {t.featuresPage.title} <span className="gradient-text">{t.featuresPage.titleHighlight}</span>
          </h1>
          <p className="text-xl text-muted-foreground max-w-2xl mx-auto">
            {t.featuresPage.subtitle}
          </p>
        </div>
      </section>

      {/* Feature Navigation */}
      <section className="sticky top-16 z-40 bg-white border-y border-border">
        <div className="max-w-7xl mx-auto px-4">
          <div className="flex overflow-x-auto py-4 gap-2 scrollbar-hide">
            {features.map((f) => {
              const FeatureIcon = (f as any).icon as any
              return (
                <button
                  key={f.id}
                  onClick={() => {
                    setActiveFeature(f.id)
                    setSelectedCapability(
                      f.id === "forecasting"
                        ? "forecastGraph"
                        : f.id === "coldchain"
                          ? "coldchainLive"
                          : null,
                    )
                  }}
                  className={cn(
                    "flex items-center gap-2 px-4 py-2 rounded-full whitespace-nowrap transition-all",
                    activeFeature === f.id
                      ? "bg-teal text-white"
                      : "bg-light-gray text-navy hover:bg-teal/10"
                  )}
                >
                  <FeatureIcon className="h-4 w-4" />
                  <span className="text-sm font-medium">{f.title}</span>
                </button>
              )
            })}
          </div>
        </div>
      </section>

      {/* Active Feature Detail */}
      <section className="py-16 px-4">
        <div className="max-w-7xl mx-auto">
          <div className="grid lg:grid-cols-2 gap-12 items-center">
            {/* Content */}
            <div className="space-y-8">
              <div className="inline-flex items-center gap-2 px-4 py-2 bg-teal/10 rounded-full">
                <ActiveIcon className="h-5 w-5 text-teal" />
                <span className="text-sm font-medium text-teal">{active.subtitle}</span>
              </div>
              <h2 className="text-3xl md:text-4xl font-bold text-navy">{active.title}</h2>
              <p className="text-lg text-muted-foreground">{active.description}</p>
              
              <div className="grid sm:grid-cols-2 gap-4">
                {active.capabilities.map((cap) => {
                  const capId = (cap as any).id ?? cap.text
                  const CapIcon = (cap as any).icon as any
                  return (
                    <button
                      key={capId}
                      type="button"
                      onClick={() => {
                        setSelectedCapability(capId)
                        if ((cap as any).id === "forecastGraph") {
                          forecastRef.current?.scrollIntoView({ behavior: "smooth", block: "center" })
                        }
                        if ((cap as any).id === "stockAnalysis") {
                          fetchStock()
                        }
                      }}
                      className={cn(
                        "flex items-center gap-3 p-4 rounded-xl transition-all text-left w-full border",
                        selectedCapability === capId
                          ? "border-[#1D9E75] bg-[#1D9E75]/10 shadow-sm"
                          : "border-transparent bg-light-gray hover:bg-[#1D9E75]/5 hover:border-[#1D9E75]/20",
                      )}
                    >
                    <div className={cn(
                      "p-2 rounded-lg",
                      active.color === "teal" ? "bg-teal/10" : "bg-amber/10"
                    )}>
                      <CapIcon className={cn(
                        "h-5 w-5",
                        active.color === "teal" ? "text-teal" : "text-amber"
                      )} />
                    </div>
                    <span className="text-sm font-medium text-navy">{cap.text}</span>
                  </button>
                )})}
              </div>

              <Button className="bg-teal text-white hover:bg-teal/90">
                {t.featuresPage.learnMore}
              </Button>
            </div>

            {/* Demo */}
            <div>
              {/* Forecasting Chart */}
              {activeFeature === "forecasting" && (() => {
                const capabilityMeta: Record<string, { title: string; description: string }> = {
                  forecastGraph: {
                    title: t.featuresPage.forecasting.chartTitle,
                    description: t.featuresPage.forecasting.description,
                  },
                  stockAnalysis: {
                    title: t.featuresPage.forecasting.cap2,
                    description: t.featuresPage.forecasting.marketNote,
                  },
                  lowStockAlert: {
                    title: t.featuresPage.forecasting.cap3,
                    description: "รายการยาที่สต็อกต่ำกว่าเกณฑ์ — คลิกเพื่อดูรายละเอียดและสั่งซื้อทันที",
                  },
                  usageTrends: {
                    title: t.featuresPage.forecasting.cap4,
                    description: "เปรียบเทียบแนวโน้มการใช้ยาจริงกับคาดการณ์ แยกตามหมวดหมู่",
                  },
                }
                const meta = capabilityMeta[selectedCapability ?? "forecastGraph"] ?? capabilityMeta.forecastGraph

                const standaloneCapabilities = ["stockAnalysis", "lowStockAlert", "usageTrends"]
                const isStandalone = standaloneCapabilities.includes(selectedCapability ?? "")

                return (
                <div
                  ref={forecastRef}
                  className={cn(
                    isStandalone ? "" : "bg-white rounded-xl p-6 shadow-lg border border-border",
                  )}
                >
                  {!isStandalone && (
                  <div className="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4 mb-4">
                    <div>
                      <h4 className="font-semibold text-navy">{meta.title}</h4>
                      <p className="text-sm text-muted-foreground mt-1">{meta.description}</p>
                    </div>
                    {selectedCapability === "forecastGraph" && (
                      <div className="flex flex-wrap items-center gap-2 text-sm">
                        <span className="flex items-center gap-2">
                          <span className="w-3 h-3 rounded-full bg-teal"></span> {t.featuresPage.forecasting.actual}
                        </span>
                        <span className="flex items-center gap-2">
                          <span className="w-3 h-3 rounded-full bg-amber"></span> {t.featuresPage.forecasting.predicted}
                        </span>
                      </div>
                    )}
                  </div>
                  )}

                  {selectedCapability === "forecastGraph" && (
                    <>
                      <div className="mb-6">
                        <div className="flex flex-wrap items-center justify-between gap-3">
                          <div className="flex items-center gap-2">
                            {(["3", "6", "9"] as const).map((range) => (
                              <button
                                key={range}
                                type="button"
                                onClick={() => setForecastRange(range)}
                                className={cn(
                                  "rounded-full px-4 py-2 text-sm font-medium transition-all border",
                                  forecastRange === range
                                    ? "bg-teal text-white border-teal"
                                    : "bg-white text-navy border-border hover:border-teal hover:bg-teal/5"
                                )}
                              >
                                {range} เดือน
                              </button>
                            ))}
                          </div>
                          <div className="flex items-center gap-3 text-sm text-muted-foreground">
                            <span>ฤดูกาล</span>
                            <input
                              type="range"
                              min={0.8}
                              max={1.2}
                              step={0.05}
                              value={seasonMultiplier}
                              onChange={(event) => setSeasonMultiplier(Number(event.target.value))}
                              className="h-2 w-40 cursor-pointer accent-teal"
                            />
                            <span className="font-semibold text-navy">x{seasonMultiplier.toFixed(2)}</span>
                          </div>
                        </div>
                      </div>

                      <ForecastChart data={displayedChartData} forecastBreakIndex={forecastBreakIndex} seasonMultiplier={seasonMultiplier} />

                      <div className="mb-4">
                        <div className="text-sm font-medium text-navy mb-2">{t.featuresPage.forecasting.drugTypeLabel}</div>
                        <p className="text-xs text-muted-foreground mb-3">{t.featuresPage.forecasting.marketNote}</p>
                        <div className="flex flex-wrap gap-2 mb-4">
                          {drugTypes.map((type) => (
                            <button
                              key={type.id}
                              onClick={() => setSelectedDrugType(type.id)}
                              className={cn(
                                "px-4 py-2 text-sm rounded-full transition-all border",
                                selectedDrugType === type.id
                                  ? "border-teal bg-teal text-white shadow-lg shadow-teal/10"
                                  : "border-transparent bg-white text-navy hover:border-teal hover:bg-teal/5"
                              )}
                            >
                              {type.label}
                            </button>
                          ))}
                        </div>
                        <div className="grid gap-3 sm:grid-cols-3">
                          <div className="rounded-3xl border border-teal/10 bg-teal/5 p-4">
                            <p className="text-xs uppercase tracking-[0.25em] text-teal">Peak</p>
                            <p className="mt-2 text-sm font-semibold text-navy">{drugTypeInfo[selectedDrugType].peak}</p>
                          </div>
                          <div className="rounded-3xl border border-teal/10 bg-white p-4">
                            <p className="text-xs uppercase tracking-[0.25em] text-teal">Volatility</p>
                            <p className="mt-2 text-sm font-semibold text-navy">{drugTypeInfo[selectedDrugType].volatility}</p>
                          </div>
                          <div className="rounded-3xl border border-teal/10 bg-white p-4">
                            <p className="text-xs uppercase tracking-[0.25em] text-teal">Insight</p>
                            <p className="mt-2 text-sm text-muted-foreground">{drugTypeInfo[selectedDrugType].note}</p>
                          </div>
                        </div>
                      </div>

                      <div className="grid gap-4 sm:grid-cols-3 mb-6">
                        <div className="rounded-3xl border border-teal/10 bg-teal/5 p-4">
                          <p className="text-xs uppercase tracking-[0.2em] text-teal">Actual</p>
                          <p className="mt-3 text-2xl font-semibold text-navy">{displayedChartData[displayedChartData.length-1].actual.toLocaleString()}</p>
                          <p className="text-xs text-muted-foreground mt-1">{displayedChartData[displayedChartData.length-1].month}</p>
                        </div>
                        <div className="rounded-3xl border border-amber/10 bg-amber/5 p-4">
                          <p className="text-xs uppercase tracking-[0.2em] text-amber-700">Predicted</p>
                          <p className="mt-3 text-2xl font-semibold text-navy">{displayedChartData[displayedChartData.length-1].predicted.toLocaleString()}</p>
                          <p className="text-xs text-muted-foreground mt-1">Next Month</p>
                        </div>
                        <div className="rounded-3xl border border-blue-100 bg-blue-50 p-4">
                          <p className="text-xs uppercase tracking-[0.2em] text-sky-700">Forecast gap</p>
                          <p className="mt-3 text-2xl font-semibold text-navy">
                            {Math.abs(displayedChartData[displayedChartData.length-1].predicted - displayedChartData[displayedChartData.length-1].actual).toLocaleString()}
                          </p>
                          <p className="text-xs text-muted-foreground mt-1">{displayedChartData[displayedChartData.length-1].predicted > displayedChartData[displayedChartData.length-1].actual ? "เพิ่มขึ้น" : "ลดลง"}</p>
                        </div>
                      </div>
                    </>
                  )}

                  {selectedCapability === "stockAnalysis" && (
                    <StockAnalysisChart data={chartData} />
                  )}

                  {selectedCapability === "lowStockAlert" && (
                    <LowStockAlerts />
                  )}

                  {selectedCapability === "usageTrends" && (
                    <UsageTrendChart data={chartData} />
                  )}
                </div>
                )
              })()}

              {activeFeature === "coldchain" && (
                <ColdChainPanel
                  view={
                    selectedCapability === "coldchainAnomaly"
                      ? "anomaly"
                      : selectedCapability === "coldchainLocation"
                        ? "shipments"
                        : "live"
                  }
                />
              )}
              {activeFeature === "diagnosis" && <DiagnosisAssistant />}
              {activeFeature === "interactions" && <DrugInteractionChecker />}
              {activeFeature === "analytics" && <AnalyticsDashboard />}
            </div>
          </div>
        </div>
      </section>

      <Footer />
    </div>
  )
}

@import url('https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700;800;900&family=DM+Sans:wght@400;500;600&family=DM+Mono:wght@400;500&display=swap');:root {

  --font-size: 16px;

  --background: #faf7f2;

  --foreground: #1a1220;

  --card: #ffffff;

  --card-foreground: #1a1220;

  --popover: #ffffff;

  --popover-foreground: #1a1220;

  --primary: #1a1220;

  --primary-foreground: #faf7f2;

  --secondary: #f0e6ff;

  --secondary-foreground: #1a1220;

  --muted: #ede8e0;

  --muted-foreground: #6b5f72;

  --accent: #ff6b35;

  --accent-foreground: #ffffff;

  --destructive: #e03131;

  --destructive-foreground: #ffffff;

  --border: rgba(26, 18, 32, 0.12);

  --input: transparent;

  --input-background: #f0ebe3;

  --switch-background: #c9bfd4;

  --font-weight-medium: 600;

  --font-weight-normal: 400;

  --ring: #ff6b35;

  --radius: 0.75rem;

  /* keep all other tokens unchanged */

} import { useState } from "react";

import { ShoppingCart, Trash2, Plus, Minus, Tag, Zap, Package } from "lucide-react";



const PRODUCTS = [

  { id: 1, name: "Laptop", price: 50000, emoji: "💻", desc: "High-performance computing" },

  { id: 2, name: "Headphones", price: 2000, emoji: "🎧", desc: "Premium audio experience" },

  { id: 3, name: "Keyboard", price: 1500, emoji: "⌨️", desc: "Mechanical precision keys" },

  { id: 4, name: "Mouse", price: 800, emoji: "🖱️", desc: "Ergonomic wireless design" },

];



const COUPONS: Record<string, number> = {

  SAVE10: 0.1,

  SAVE20: 0.2,

};



type Cart = Record<number, number>;



function formatINR(n: number) {

  return "₹" + n.toLocaleString("en-IN");

}



function calcCashback(total: number) {

  if (total >= 100000) return total * 0.15;

  if (total >= 50000) return total * 0.1;

  if (total >= 20000) return total * 0.05;

  return 0;

}



function CashbackBadge({ total }: { total: number }) {

  if (total >= 100000)

    return <span className="text-xs font-mono font-medium bg-emerald-100 text-emerald-700 px-2 py-0.5 rounded-full">15% cashback unlocked!</span>;

  if (total >= 50000)

    return <span className="text-xs font-mono font-medium bg-emerald-100 text-emerald-700 px-2 py-0.5 rounded-full">10% cashback unlocked!</span>;

  if (total >= 20000)

    return <span className="text-xs font-mono font-medium bg-emerald-100 text-emerald-700 px-2 py-0.5 rounded-full">5% cashback unlocked!</span>;

  const needed = 20000 - total;

  return (

    <span className="text-xs font-mono text-muted-foreground">

      Add {formatINR(needed)} more for 5% cashback

    </span>

  );

}



function MemphisShapes() {

  return (

    <div className="absolute inset-0 overflow-hidden pointer-events-none" aria-hidden>

      <div className="absolute top-8 right-16 w-16 h-16 rounded-full border-4 border-accent/30" />

      <div className="absolute top-20 right-8 w-8 h-8 bg-accent/15 rotate-45" />

      <div className="absolute bottom-12 left-12 w-12 h-12 rounded-full bg-purple-200/40" />

      <div className="absolute bottom-6 left-32 w-6 h-6 border-4 border-purple-400/30 rotate-12" />

      <div className="absolute top-1/2 left-4 w-4 h-16 bg-amber-200/50 rounded-full -translate-y-1/2" />

    </div>

  );

}



export default function App() {

  const [cart, setCart] = useState<Cart>({});

  const [view, setView] = useState<"shop" | "cart">("shop");

  const [couponInput, setCouponInput] = useState("");

  const [appliedCoupon, setAppliedCoupon] = useState<{ code: string; rate: number } | null>(null);

  const [couponError, setCouponError] = useState("");



  const cartCount = Object.values(cart).reduce((a, b) => a + b, 0);



  function addToCart(id: number) {

    setCart((prev) => ({ ...prev, [id]: (prev[id] || 0) + 1 }));

  }



  function removeOne(id: number) {

    setCart((prev) => {

      const next = { ...prev };

      if (next[id] > 1) next[id]--;

      else delete next[id];

      return next;

    });

  }



  function removeItem(id: number) {

    setCart((prev) => {

      const next = { ...prev };

      delete next[id];

      return next;

    });

  }



  function applyCoupon() {

    const code = couponInput.trim().toUpperCase();

    if (!code) return;

    if (COUPONS[code]) {

      setAppliedCoupon({ code, rate: COUPONS[code] });

      setCouponError("");

    } else {

      setCouponError("Invalid coupon code");

      setAppliedCoupon(null);

    }

  }



  function removeCoupon() {

    setAppliedCoupon(null);

    setCouponInput("");

    setCouponError("");

  }



  const cartItems = Object.entries(cart)

    .map(([id, qty]) => ({ product: PRODUCTS.find((p) => p.id === Number(id))!, qty }))

    .filter((x) => x.product);



  const subtotal = cartItems.reduce((sum, { product, qty }) => sum + product.price * qty, 0);

  const cashback = calcCashback(subtotal);

  const couponDiscount = appliedCoupon ? subtotal * appliedCoupon.rate : 0;

  const finalAmount = subtotal - cashback - couponDiscount;



  return (

    <div className="min-h-screen bg-background" style={{ fontFamily: "'DM Sans', sans-serif" }}>

      {/* Header */}

      <header className="sticky top-0 z-20 bg-primary text-primary-foreground shadow-lg">

        <div className="max-w-5xl mx-auto px-4 py-4 flex items-center justify-between">

          <div className="flex items-center gap-3">

            <div className="w-9 h-9 bg-accent rounded-lg flex items-center justify-center">

              <Package size={18} className="text-white" />

            </div>

            <span style={{ fontFamily: "'Nunito', sans-serif", fontWeight: 800, fontSize: "1.3rem", letterSpacing: "-0.02em" }}>

              ShopKart

            </span>

          </div>



          <nav className="flex items-center gap-2">

            <button

              onClick={() => setView("shop")}

              className={`px-4 py-2 rounded-lg text-sm font-semibold transition-all ${

                view === "shop"

                  ? "bg-accent text-white"

                  : "bg-white/10 hover:bg-white/20 text-primary-foreground"

              }`}

            >

              Products

            </button>

            <button

              onClick={() => setView("cart")}

              className={`relative px-4 py-2 rounded-lg text-sm font-semibold transition-all flex items-center gap-2 ${

                view === "cart"

                  ? "bg-accent text-white"

                  : "bg-white/10 hover:bg-white/20 text-primary-foreground"

              }`}

            >

              <ShoppingCart size={16} />

              Cart

              {cartCount > 0 && (

                <span className="absolute -top-1.5 -right-1.5 w-5 h-5 bg-amber-400 text-primary rounded-full text-[10px] font-black flex items-center justify-center">

                  {cartCount}

                </span>

              )}

            </button>

          </nav>

        </div>

      </header>



      <main className="max-w-5xl mx-auto px-4 py-8">

        {/* Products View */}

        {view === "shop" && (

          <div>

            <div className="mb-8 relative">

              <MemphisShapes />

              <div className="relative z-10">

                <h1

                  className="text-foreground mb-1"

                  style={{ fontFamily: "'Nunito', sans-serif", fontWeight: 900, fontSize: "2.2rem", letterSpacing: "-0.03em" }}

                >

                  Available Products

                </h1>

                <p className="text-muted-foreground text-sm">Add items to your cart and enjoy cashback rewards</p>

              </div>

            </div>



            <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">

              {PRODUCTS.map((product) => {

                const inCart = cart[product.id] || 0;

                const stripeColors = ["#ff6b35", "#7c3aed", "#f59e0b", "#059669"];

                return (

                  <div

                    key={product.id}

                    className="bg-card rounded-2xl border border-border overflow-hidden hover:shadow-md transition-shadow"

                  >

                    <div className="h-2" style={{ background: stripeColors[product.id - 1] }} />

                    <div className="p-5">

                      <div className="text-4xl mb-3">{product.emoji}</div>

                      <h3

                        className="text-foreground mb-0.5"

                        style={{ fontFamily: "'Nunito', sans-serif", fontWeight: 800 }}

                      >

                        {product.name}

                      </h3>

                      <p className="text-muted-foreground text-xs mb-3">{product.desc}</p>

                      <div

                        className="text-lg font-black mb-4 text-foreground"

                        style={{ fontFamily: "'DM Mono', monospace" }}

                      >

                        {formatINR(product.price)}

                      </div>



                      {inCart === 0 ? (

                        <button

                          onClick={() => addToCart(product.id)}

                          className="w-full bg-primary text-primary-foreground rounded-xl py-2.5 text-sm font-semibold hover:bg-accent hover:text-white transition-colors"

                        >

                          Add to Cart

                        </button>

                      ) : (

                        <div className="flex items-center justify-between bg-secondary rounded-xl px-3 py-1.5">

                          <button

                            onClick={() => removeOne(product.id)}

                            className="w-7 h-7 flex items-center justify-center rounded-lg bg-white hover:bg-red-100 transition-colors border border-border"

                          >

                            <Minus size={14} />

                          </button>

                          <span className="font-black text-foreground" style={{ fontFamily: "'DM Mono', monospace" }}>

                            {inCart}

                          </span>

                          <button

                            onClick={() => addToCart(product.id)}

                            className="w-7 h-7 flex items-center justify-center rounded-lg bg-primary text-primary-foreground hover:bg-accent transition-colors"

                          >

                            <Plus size={14} />

                          </button>

                        </div>

                      )}

                    </div>

                  </div>

                );

              })}

            </div>



            {cartCount > 0 && (

              <div className="mt-8 flex justify-center">

                <button

                  onClick={() => setView("cart")}

                  className="bg-accent text-white px-8 py-3 rounded-2xl font-bold text-base hover:opacity-90 transition-opacity flex items-center gap-2 shadow-lg"

                  style={{ fontFamily: "'Nunito', sans-serif" }}

                >

                  <ShoppingCart size={18} />

                  View Cart ({cartCount} item{cartCount > 1 ? "s" : ""})

                </button>

              </div>

            )}

          </div>

        )}



        {/* Cart View */}

        {view === "cart" && (

          <div className="max-w-2xl mx-auto">

            <h1

              className="text-foreground mb-6"

              style={{ fontFamily: "'Nunito', sans-serif", fontWeight: 900, fontSize: "2rem", letterSpacing: "-0.03em" }}

            >

              Your Cart

            </h1>



            {cartItems.length === 0 ? (

              <div className="text-center py-20 bg-card rounded-2xl border border-border">

                <div className="text-6xl mb-4">🛒</div>

                <p className="text-muted-foreground font-semibold" style={{ fontFamily: "'Nunito', sans-serif" }}>

                  Your cart is empty

                </p>

                <button

                  onClick={() => setView("shop")}

                  className="mt-4 bg-primary text-primary-foreground px-6 py-2.5 rounded-xl text-sm font-semibold hover:bg-accent transition-colors"

                >

                  Browse Products

                </button>

              </div>

            ) : (

              <div className="space-y-3">

                {/* Cart items */}

                {cartItems.map(({ product, qty }) => (

                  <div key={product.id} className="bg-card rounded-2xl border border-border p-4 flex items-center gap-4">

                    <div className="text-3xl w-12 h-12 flex items-center justify-center bg-secondary rounded-xl shrink-0">

                      {product.emoji}

                    </div>

                    <div className="flex-1 min-w-0">

                      <p className="font-bold text-foreground truncate" style={{ fontFamily: "'Nunito', sans-serif" }}>

                        {product.name}

                      </p>

                      <p className="text-sm text-muted-foreground" style={{ fontFamily: "'DM Mono', monospace" }}>

                        {formatINR(product.price)} × {qty}

                      </p>

                    </div>



                    <div className="flex items-center gap-2">

                      <div className="flex items-center gap-1.5 bg-secondary rounded-xl px-2 py-1">

                        <button

                          onClick={() => removeOne(product.id)}

                          className="w-6 h-6 flex items-center justify-center rounded-lg bg-white hover:bg-red-100 transition-colors border border-border"

                        >

                          <Minus size={12} />

                        </button>

                        <span className="w-6 text-center text-sm font-black" style={{ fontFamily: "'DM Mono', monospace" }}>

                          {qty}

                        </span>

                        <button

                          onClick={() => addToCart(product.id)}

                          className="w-6 h-6 flex items-center justify-center rounded-lg bg-primary text-primary-foreground hover:bg-accent transition-colors"

                        >

                          <Plus size={12} />

                        </button>

                      </div>



                      <span className="text-sm font-black text-foreground min-w-[72px] text-right" style={{ fontFamily: "'DM Mono', monospace" }}>

                        {formatINR(product.price * qty)}

                      </span>



                      <button

                        onClick={() => removeItem(product.id)}

                        className="w-8 h-8 flex items-center justify-center rounded-xl text-muted-foreground hover:text-destructive hover:bg-red-50 transition-colors"

                      >

                        <Trash2 size={15} />

                      </button>

                    </div>

                  </div>

                ))}



                {/* Cashback info */}

                <div className="bg-emerald-50 border border-emerald-200 rounded-2xl p-4 flex items-start gap-3">

                  <Zap size={18} className="text-emerald-600 mt-0.5 shrink-0" />

                  <div>

                    <p className="text-sm font-semibold text-emerald-800 mb-0.5">Cashback Reward</p>

                    <CashbackBadge total={subtotal} />

                  </div>

                </div>



                {/* Coupon */}

                <div className="bg-card border border-border rounded-2xl p-4">

                  <div className="flex items-center gap-2 mb-3">

                    <Tag size={16} className="text-accent" />

                    <span className="font-bold text-foreground text-sm" style={{ fontFamily: "'Nunito', sans-serif" }}>

                      Coupon Code

                    </span>

                  </div>



                  {appliedCoupon ? (

                    <div className="flex items-center justify-between bg-emerald-50 border border-emerald-200 rounded-xl px-3 py-2.5">

                      <span className="text-sm font-semibold text-emerald-700">

                        <span className="font-mono">{appliedCoupon.code}</span> — {(appliedCoupon.rate * 100).toFixed(0)}% off

                      </span>

                      <button onClick={removeCoupon} className="text-xs text-muted-foreground hover:text-destructive transition-colors">

                        Remove

                      </button>

                    </div>

                  ) : (

                    <div className="flex gap-2">

                      <input

                        type="text"

                        value={couponInput}

                        onChange={(e) => { setCouponInput(e.target.value.toUpperCase()); setCouponError(""); }}

                        onKeyDown={(e) => e.key === "Enter" && applyCoupon()}

                        placeholder="Enter code (SAVE10, SAVE20)"

                        className="flex-1 bg-input-background border border-border rounded-xl px-3 py-2 text-sm outline-none focus:ring-2 focus:ring-accent/40 font-mono placeholder:text-muted-foreground placeholder:font-sans"

                      />

                      <button

                        onClick={applyCoupon}

                        className="bg-primary text-primary-foreground px-4 py-2 rounded-xl text-sm font-semibold hover:bg-accent transition-colors"

                      >

                        Apply

                      </button>

                    </div>

                  )}

                  {couponError && <p className="text-destructive text-xs mt-1.5">{couponError}</p>}

                </div>



                {/* Bill summary */}

                <div className="bg-primary text-primary-foreground rounded-2xl p-5">

                  <h2 className="font-black text-lg mb-4" style={{ fontFamily: "'Nunito', sans-serif" }}>

                    Order Summary

                  </h2>

                  <div className="space-y-2.5 text-sm">

                    <div className="flex justify-between">

                      <span className="text-white/70">Subtotal</span>

                      <span className="font-mono font-semibold">{formatINR(subtotal)}</span>

                    </div>

                    {cashback > 0 && (

                      <div className="flex justify-between text-emerald-400">

                        <span>Cashback</span>

                        <span className="font-mono font-semibold">− {formatINR(cashback)}</span>

                      </div>

                    )}

                    {couponDiscount > 0 && (

                      <div className="flex justify-between text-amber-400">

                        <span>Coupon ({appliedCoupon?.code})</span>

                        <span className="font-mono font-semibold">− {formatINR(couponDiscount)}</span>

                      </div>

                    )}

                    <div className="border-t border-white/20 pt-2.5 flex justify-between text-base">

                      <span className="font-bold">Total Payable</span>

                      <span className="font-black text-accent" style={{ fontFamily: "'DM Mono', monospace", fontSize: "1.1rem" }}>

                        {formatINR(Math.round(finalAmount))}

                      </span>

                    </div>

                  </div>



                  <button className="w-full mt-5 bg-accent text-white py-3 rounded-xl font-bold text-base hover:opacity-90 transition-opacity">

                    Proceed to Checkout

                  </button>

                </div>

              </div>

            )}

          </div>

        )}

      </main>

    </div>

  );

}
